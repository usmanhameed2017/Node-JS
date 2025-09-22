# ⚡ SOCKET.IO

- **Socket.IO** is a JavaScript library that helps in real-time communication between a server and clients (like web browsers or apps).

- It allows data to be sent and received instantly without refreshing the page.

- Here are some key points about Socket.IO:

### Real-time Communication

- It allows instant data exchange between users, making it perfect for chat apps, live notifications, and multiplayer games.

### Two-way Connection

- Both the server and client can send and receive messages at the same time.

### Fast and Efficient

- It reduces delays, making apps more interactive and responsive.

### Automatic Reconnection

- If the connection is lost, it tries to reconnect automatically.

### Easy to Use

- Provides simple methods to send and receive messages.

### Supports Rooms & Namespaces
 
- Allows grouping of users in different chat rooms or sections.

> Note: Since WebSocket relies on the **HTTP protocol** internally, you need to use the **HTTP module** to create a server. This means you must bind your **Express server** with the **Socket.IO server** to ensure proper communication.

## PRACTICAL IMPLEMENTATION OF CHAT APP USING SOCKET.IO

### SERVER SIDE

#### INSTALLATION

- To install **socket.io** in your Node.js application; type this command in terminal.

```javascript
npm i socket.io
```

---

#### SETUP

**src/app.js**
```javascript
const express = require("express");
const http = require("http");
const { Server } = require("socket.io");
const connectSocket = require("./service/socket");

// Cors options
const corsOptions = {
    origin:origin,
    credentials:true,
    methods:["GET", "POST", "PUT", "PATCH", "DELETE"],
    allowedHeaders:["Content-Type", "Authorization"]
};

// Express app
const app = express();

// Create HTTP Server
const server = http.createServer(app);

// Bind web socket server with express server
const io = new Server(server, { cors: corsOptions });  // Socket instance with cors option

// ************* MIDDLEWARES ************* //
app.use(cors(corsOptions));
app.use(express.urlencoded({ extended:false }));
app.use(express.json());

// Socket middleware for authentication
io.use(socketAuthentication);

// Socket connection
connectSocket(io);

// Start server
server.listen(8000, () => console.log("Server is started and running at http://localhost:8000"));
```

---

**src/middleware/socket.js**

```javascript
const jwt = require("jsonwebtoken");

// Establish socket connection only when user is authenticated
const socketAuthentication = (socket, next) => {
    try 
    {
        const { authToken } = socket.handshake.auth;
        if(!authToken) return next(new Error("Auth token is missing"));

        const user = jwt.verify(authToken, process.ENV.JWT_SECRET);
        if(!user) return next(new Error("Invalid access token"));

        socket.user = user || null;
        return next();         
    } 
    catch (error) 
    {
        console.log(error.message);
    }
}
```

---

**src/service/socket.js**
```javascript
const Group = require("../models/group");
const User = require("../models/user");

// Global store (in-memory map)
const onlineUsers = new Map();

// ************* SOCKET CONNECTION ************* //
const connectSocket = (io) => {
    io.on("connection", async (socket) => {  
        // Extract user object
        const { user } = socket;

        // Validate user
        if(!user) return;
        
        // Save mapping
        onlineUsers.set(user._id, socket.id);  

        // Update status in DB
        const data = await User.findByIdAndUpdate(user._id, { onlineStatus:"Online" }, { new:true });
        io.emit("user-online", data);
        console.log("New client connected:", socket.id);

        // Handle private messages (One to One)
        socket.on("private-message", (data) => {
            const { from, to } = data;

            // Get socket IDs for sending messages to specific socket
            const receiverSocketId = onlineUsers.get(to); 
            if(receiverSocketId) io.to(receiverSocketId).emit("private-message", data);

            const senderSocketId = onlineUsers.get(from);
            if(senderSocketId) io.to(senderSocketId).emit("private-message", data);           
        });

        // Handle group messages
        socket.on("group-message", async (data) => {
            const { conversationId } = data;

            // Find group members
            const group = await Group.findById(conversationId).lean();
            if (!group) return;

            // Broadcast to all group members
            group.members.forEach((memberId) => {
                const sockets = onlineUsers.get(memberId.toString()) || [];
                sockets.forEach((id) => io.to(id).emit("group-message", data));
            });
        });       

        // Disconnect
        socket.on("disconnect", async () => {
            if(!user) return;
            
            // Remove mapping from online users
            onlineUsers.delete(user._id);

            // Update status in DB
            const data = await User.findByIdAndUpdate(user._id, { onlineStatus: "Offline" }, { new: true });
            io.emit("user-offline", data);            
            console.log("Socket disconnected", socket.id);
        });
    });    
};

module.exports = connectSocket;
```

> Note: If you want to allow same account to be logged-in within multiple devices and let the realtime messages appear on multiple devices to same user, then use this setup instead.

**src/service/socket.js**
```javascript
const Group = require("../models/group");
const User = require("../models/user");

// Global store (in-memory map)
const onlineUsers = new Map();

// ************* SOCKET CONNECTION ************* //
const connectSocket = (io) => {
    io.on("connection", async (socket) => {
        // Extract user object
        const { user } = socket;

        // User login
        if(!user) return;

        // Save mapping (Multiple device login)
        if(!onlineUsers.has(user._id)) onlineUsers.set(user._id, new Set());
        onlineUsers.get(user._id).add(socket.id);     

        // // Update status in DB
        const data = await User.findByIdAndUpdate(user._id, { onlineStatus:"Online" }, { new:true });
        io.emit("user-online", data);
        console.log("New client connected:", socket.id);       

        // Handle private messages (One to One)
        socket.on("private-message", (data) => {
            const { from, to } = data;

            // Get socket IDs of receiver and sender
            const receiverSockets = onlineUsers.get(to) || [];
            receiverSockets.forEach((id) => io.to(id).emit("private-message", data));

            const senderSockets = onlineUsers.get(from) || [];
            senderSockets.forEach((id) => io.to(id).emit("private-message", data));            
        });

        // Handle group messages
        socket.on("group-message", async (data) => {
            const { conversationId } = data;

            // Find group members
            const group = await Group.findById(conversationId).lean();
            if (!group) return;

            // Broadcast to all group members
            group.members.forEach((memberId) => {
                const sockets = onlineUsers.get(memberId.toString()) || [];
                sockets.forEach((id) => io.to(id).emit("group-message", data));
            });
        });       

        // Disconnect
        socket.on("disconnect", async () => {
            if(!user) return;

            // Remove mapping from online users
            const sockets = onlineUsers.get(user._id);
            if(sockets) 
            {
                sockets.delete(socket.id);

                // If no socket online with their respective user ID
                if(sockets.size === 0)
                {
                    // Delete online user from map
                    onlineUsers.delete(user._id);

                    // Update status in DB
                    const data = await User.findByIdAndUpdate(user._id, { onlineStatus: "Offline" }, { new: true });
                    io.emit("user-offline", data);
                }
            }

            // Update status in DB
            const data = await User.findByIdAndUpdate(user._id, { onlineStatus: "Offline" }, { new: true });
            io.emit("user-offline", data);            
            console.log("Socket disconnected", socket.id);
        });
    });    
};

module.exports = connectSocket;
```

---

**src/models/chat.js**
```javascript
const { Schema, model } = require("mongoose");

// Schema
const chatSchema = new Schema({
    from:{
        type:Schema.Types.ObjectId,
        ref:"User",
        required:true
    },
    to:{
        type:Schema.Types.ObjectId,
        ref:"User",
        default:null
    },
    message:{
        type:String,
        required:true
    },
    isRead:{
        type:Boolean,
        default:false
    },
    conversationId: { 
        type:String, 
        index: true,
        required:true
    }
}, { timestamps:true });

// Model
const Chat = model("Chat", chatSchema);

module.exports = Chat;
```

---

**src/models/group.js**
```javascript
const { Schema, model, Types } = require("mongoose");

// Schema
const groupSchema = new Schema({
    name:{
        type:String,
        trim:true,
        required:true
    },
    members:[{
        type:Schema.Types.ObjectId,
        ref:"User",
        required: true
    }],
    lastMessage:{  // Pointer to latest message
        type: Schema.Types.ObjectId,
        ref: "Chat",
        default: null,
    },
    createdBy:{
        type:Schema.Types.ObjectId,
        ref:"User",
        required:true
    }     
}, { timestamps:true });

// Pre save hook
groupSchema.pre("save", function(next) {
    if(!this.members) this.members = [];
    const ids = this.members.map(id => id.toString());

    // Always add group creator into the group which he created
    if(!ids.includes(this.createdBy.toString())) ids.push(this.createdBy.toString());
    
    // Convert back to ObjectId
    const unique = [...new Set(ids)].map(id => new Types.ObjectId(id));
    this.members = unique;
    return next();
});

// Model
const Group = model("Group", groupSchema);

module.exports = Group;
```

---

**src/routes/chat.js**
```javascript
const { sendPrivateMessage, sendGroupMessage } = require("../controllers/chat");
const { authentication } = require("../middlewares/auth");

// Router instance
const chatRouter = require("express").Router();

// Send private message
chatRouter.route("/private-message").post(authentication, sendPrivateMessage);

// Send group message
chatRouter.route("/group-message").post(authentication, sendGroupMessage);

module.exports = chatRouter;
```

---

**src/controllers/chat.js**
```javascript
const Chat = require("../models/chat");
const Group = require("../models/group");
const ApiError = require("../utils/ApiError");
const ApiResponse = require("../utils/ApiResponse");
const generateConversationId = require("../utils/generateConversationId");

// Send private message (one to one chat)
const sendPrivateMessage = async (request, response) => {
    const senderId = request.user?._id;
    const receiverId = request.body?.to || null;
    request.body.from = senderId;

    // Validate reciever
    if(!receiverId) throw new ApiError(400, "Receiver ID is missing");

    // Generate unique id for conversation thread
    request.body.conversationId = generateConversationId(senderId, receiverId);

    try 
    {
        const chat = await Chat.create(request.body);
        if(!chat) throw new ApiError(400, "Failed to send message");
        return response.status(200).json(new ApiResponse(200, chat, "Message has been sent"));
    } 
    catch(error) 
    {
        throw error;
    }
};

// Send group message (one to one chat)
const sendGroupMessage = async (request, response) => {
    request.body.from = request.user?._id;

    // Validate group ID
    if(!request.body?.conversationId) throw new ApiError(400, "Group ID is missing");

    try 
    {
        // Find group
        const group = await Group.findById(request.body?.conversationId);

        // If the sender is not a member of a group
        if(!group.members.includes(request.user?._id)) throw new ApiError(400, "Only members can send a mesage");

        const chat = await Chat.create(request.body);
        if(!chat) throw new ApiError(400, "Failed to send message");

        // Get chat data with sender name
        const chatData = await Chat.findById(chat._id).populate("from", "name");
        return response.status(200).json(new ApiResponse(200, chatData, "Message has been sent"));
    } 
    catch(error) 
    {
        throw error;
    }
};

module.exports = { sendPrivateMessage, sendGroupMessage };
```

---

**src/utils/generateConversationId.js**
```javascript
const generateConversationId = (senderId, receiverId) => {
    return [senderId.toString(), receiverId.toString()].sort().join("-");
};

module.exports = generateConversationId;
```

---

### CLIENT SIDE

#### INSTALLATION

- To install **socket.io** in your React.js application; type this command in terminal.

```javascript
npm i socket.io-client
```

---

#### SETUP

**src/service/socket.js**
```javascript
import { io } from "socket.io-client";
import { backendURL } from "../constants";

// Configuration
const socket = io(backendURL, { 
    withCredentials: true, 
    autoConnect: false // Connect socket after login
});

// Connect socket
export const connectSocket = (authToken = null) => {
    try 
    {
        // Destroy previous instance and event listeners if exist
        if(socket.connected)
        {
            socket.removeAllListeners();
            socket.disconnect();
        }
        
        // Connect socket only if user is login
        if(authToken)
        {
            socket.auth = { accessToken: authToken };
            socket.connect();
        }
    } 
    catch(error) 
    {
       console.log("Failed to connect socket", error.message);
    }
};

// Disconnect socket
export const disconnectSocket = () => {
    try 
    {
        if(socket.connected)
        {
            socket.removeAllListeners();
            socket.disconnect();
        }
    } 
    catch(error) 
    {
        console.log("Failed to disconnect socket", error.message);
    }
};

export default socket;
```

---

**src/context/auth.js**
```javascript
import { useState, useEffect, createContext, useContext, useCallback } from 'react';
import { useNavigate } from 'react-router-dom';
import axios from "axios";
import { connectSocket, disconnectSocket } from '../service/socket';

// Create auth context
const AuthContext = createContext();

function AuthProvider({ children })
{
    // Global states
    const [user, setUser] = useState(null);
    const [isLoggedIn, setLoggedIn] = useState(null); 

    // For navigation
    const navigate = useNavigate();

    // Signup
    const userSignup = useCallback(async (user, action) => {
        try 
        {
            await axios.post("/auth/user/signup", user);
            action.resetForm();
            navigate("/login");
        } 
        catch(error) 
        {
            return error;
        }
    },[]);

    // User Login
    const userLogin = useCallback(async (user, action) => {
        try
        {
            const response = await axios.post("/auth/user/login", user);
            connectSocket(response.data?.accessToken); // Connect socket
            setUser(response.data.user);
            setLoggedIn(response.success);
            action.resetForm();
            navigate('/home');
        }
        catch(error)
        {
            return error;
        }
    },[]);

    // User Logout
    const userLogout = useCallback(async () => {
        try 
        {
            await axios.get("/auth/user/logout");
            disconnectSocket(); // Disconnect socket

            setUser(null);
            setLoggedIn(false);
            navigate("/", { replace:true });
        } 
        catch (error) 
        {
           return error;
        }
    },[]);   

    // Verify Access Token
    const verifyAccessToken = useCallback(async () => {
        try 
        {
            const response = await axios.get("/auth/user/verifyAccessToken");
            connectSocket(response.data?.accessToken); // Connect socket
            setUser(response.data.user);
            setLoggedIn(response.success); 
        } 
        catch(error) 
        {
            disconnectSocket(); // Disconnect socket
            setUser(null);
            setLoggedIn(false);
            return error;
        }
    },[]);

    // On app load
    useEffect(() => {
        verifyAccessToken();
    },[]);

    return(
        <AuthContext.Provider value={{ userSignup, userLogin,  userLogout, 
        isLoggedIn, setLoggedIn, user, setUser }}>
            { children }
        </AuthContext.Provider>
    );
}

// Custom hook
export const useAuth = () => useContext(AuthContext);

export default AuthProvider;
```

---

**src/hooks/socket.js**
```javascript
import { useEffect } from "react";
import socket from "../service/socket";

function useSocket(event, callback)
{
    useEffect(() => {
        if(!socket || !event || typeof callback !== "function") return;

        // Listen for event
        socket?.on(event, callback);

        // Cleanup on unmount
        return () => socket?.off(event, callback);
    }, [event, callback]);
}

export default useSocket;
```

---

**src/components/PrivateChats.jsx**
```javascript
import { useCallback, useEffect, useState } from "react";
import styles from "./style.module.css";
import { useChat } from "../../context/chat";
import useSocket from "../../hooks/useSocket";
import axios from "axios";

function PrivateChats() 
{
    // Global States
    const { selectedUser, privateMessages, setPrivateMessages } = useChat();
    const [error, setError] = useState("");

    // Fetch chats
    useEffect(() => {
        if(!selectedUser?._id) return;
        setPrivateMessages([]);
        setError("");

        axios.get(`/chat/private-message/${selectedUser?._id}`);
        .then((response) => setPrivateMessages(response.data))
        .catch((error) => setError(error.message));
    }, [selectedUser?._id]); 

    // Handle incoming messages in real time
    const incomingMessages = useCallback((data) => {
        // Only accept messages if they belong to the current chat
        if(data.from === selectedUser?._id || data.to === selectedUser?._id) 
        {
            setPrivateMessages((prev) => [...prev, data]);
        }
    }, [selectedUser?._id]);

    // Listen for incoming messages in real time
    useSocket("private-message", incomingMessages);

    return(
        <>
            {/* UI Design Here */}
        </>
    );
}

export default PrivateChats;
```

---

**src/components/GroupMessage.jsx**
```javascript
import { useCallback, useEffect, useState } from "react";
import styles from "./style.module.css";
import { useChat } from "../../context/chat";
import useSocket from "../../hooks/useSocket";
import axios from "axios";

function GroupChats() 
{
    // Global States
    const { selectedGroup, groupMessages, setGroupMessages } = useChat();
    const [error, setError] = useState("");

    // Fetch chats
    useEffect(() => {
        if(!selectedGroup?._id) return;
        setGroupMessages([]);
        setError("");

        axios.get(`/chat/group-message/${selectedGroup?._id}`)
        .then((response) => setGroupMessages(response.data))
        .catch((error) => setError(error.message));
    }, [selectedGroup?._id]); 

    // Handle incoming messages in real time
    const incomingMessages = useCallback((data) => {
        // Accept only currect selected group mesages
        if(data.conversationId === selectedGroup?._id) 
        {
            setGroupMessages((prev) => [...prev, data]);
        }
    }, [selectedGroup?._id]);

    // Listen for incoming messages in real time
    useSocket("group-message", incomingMessages);

    return(
        <>
            {/* UI Design Here */}            
        </>
    );
}

export default GroupChats;
```
