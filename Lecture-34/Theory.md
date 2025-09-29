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
const socketAuthentication = require("./middlewares/socket");
const SocketIOService = require("./service/socket");

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
const socketio = new SocketIOService(io);
socketio.connect();

// ************* IMPORT ROUTES ************* //
const chatRouter = require("./routes/chat");
const groupRouter = require("./routes/group");

// Registered routes
app.use("/api/v1/chat", chatRouter);
app.use("/api/v1/group", groupRouter);

// Start server
server.listen(8000, () => console.log("Server is started and running at http://localhost:8000"));
```

---

**src/middleware/socket.js**

```javascript
const ApiError = require("../utils/ApiError");
const { verifyAccessToken } = require("../utils/auth");

// Socket authentication
const socketAuthentication = (socket, next) => {
    try 
    {
        const { authToken } = socket.handshake.auth;
        if(!authToken) throw new ApiError(401, "Auth token is missing");

        const user = verifyAccessToken(authToken);
        if(!user) throw new ApiError(400, "Invalid access token");

        socket.user = user || null;
        return next();    
    } 
    catch (error) 
    {
        console.log(error.message);
    }
};

module.exports = socketAuthentication;
```

---

**src/service/socket.js**
```javascript
const User = require("../models/user");
const Group = require("../models/group");

// Socket Service Blue Print
class SocketIOService
{
    // Constructor
    constructor(io)
    {
        this.io = io;
    }

    // Socket connection
    connect()
    {
        this.io.on("connection", async (socket) => {
            // Join public room
            socket.join("public");
            console.log("New client connected:", socket.id);

            // Extract user object
            const { user } = socket;

            // Validate user
            if(!user) return;
            
            // Join private room for user (one-to-one chat)
            const roomId = `user:${user._id}`;
            socket.join(roomId);

            // Join all group rooms
            const groups = await Group.find({ members: user._id }).select("_id");  
            groups.forEach(group => socket.join(`group:${group._id}`));            

            // Mark status in DB as online if the first socket is connected that associated with the room ID
            const roomSockets = await this.io.in(roomId).fetchSockets();
            if(roomSockets.length === 1)
            {
                try 
                {
                    const data = await User.findByIdAndUpdate(user._id, { onlineStatus:"Online" }, { new:true });
                    this.io.to("public").emit("user-online", data);
                } 
                catch(error) 
                {
                    console.log(`Failed to update status: ${error.message}`);
                }
            }

            // Disconnect
            socket.on("disconnect", async () => {
                // Validate user
                if(!user) return;

                // Mark status in DB as offline if there's no sockets remaining that associated with the room ID
                const remainingRoomSockets = await this.io.in(roomId).fetchSockets();
                if(remainingRoomSockets.length === 0)
                {
                    try 
                    {
                        const data = await User.findByIdAndUpdate(user._id, { onlineStatus: "Offline" }, { new: true });
                        this.io.to("public").emit("user-offline", data);
                    } 
                    catch(error) 
                    {
                        console.log(`Failed to update status: ${error.message}`);
                    }
                }
                console.log("Client disconnected", socket.id);
            });
        });    
    }

    // Broadcast to public room sockets
    public(emitMessage, payload = null)
    {
        // Validate
        if(!this.io) return console.log("IO instance is missing");
        if(!emitMessage) return console.log("Please specify socket emitter message");
        
        // Emit for public sockets
        this.io.to("public").emit(emitMessage, payload);
    }

    // Broadcast to private room sockets (One to one)
    private(from, to, emitMessage, payload = null) 
    {
        // Validate
        if(!this.io) return console.log("IO instance is missing");
        if(!from) return console.log("Sender socket is missing");
        if(!to) return console.log("Receiver socket is missing");
        if(!emitMessage) return console.log("Please specify socket emitter message");

        // Emit for sender and receiver only
        this.io.to(`user:${to}`).to(`user:${from}`).emit(emitMessage, payload);
    }

    // Broadcast to group room sockets
    group(groupId, emitMessage, payload = null)
    {
        // Validate
        if(!this.io) return console.log("IO instance is missing");
        if(!emitMessage) return console.log("Please specify socket emitter message");

        // Emit for group members only
        this.io.to(`group:${groupId}`).emit(emitMessage, payload);
    }
}

module.exports = SocketIOService;
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
const SocketIOService = require("../service/socket");

// Send private message (one-to-one chat)
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
        // Insert into database
        const chat = await Chat.create(request.body);
        if(!chat) throw new ApiError(400, "Failed to send message");

        // Broadcast
        const broadcast = new SocketIOService(request.io);
        broadcast.private(senderId, receiverId, "private-message", chat);

        // Response
        return response.status(200).json(new ApiResponse(200, chat, "Message has been sent"));
    } 
    catch(error) 
    {
        throw error;
    }
};

// Send group message
const sendGroupMessage = async (request, response) => {
    request.body.from = request.user?._id;

    // Validate group ID
    if(!request.body?.conversationId) throw new ApiError(400, "Group ID is missing");

    try 
    {
        // Find group
        const group = await Group.findById(request.body?.conversationId);
        if(!group) throw new ApiError(404, "Group not found");

        // If the sender is not a member of a group
        const isMember = group.members.some(memberId => memberId.toString() === request.user._id.toString());
        if(!isMember) throw new ApiError(400, "Only members can send a message");

        // Insert into database and get data with sender name
        const chat = await (await Chat.create(request.body)).populate("from", "name");
        if(!chat) throw new ApiError(400, "Failed to send message");

        // Update group's last message with the latest message
        group.lastMessage = chat?._id;
        await group.save();

        // Broadcast to all group members
        const broadcast = new SocketIOService(request.io);
        broadcast.group(group._id, "group-message", chat);

        // Response
        return response.status(200).json(new ApiResponse(200, chat, "Message has been sent"));
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

**src/context/chat.jsx**
```javascript
import { useState, createContext, useContext, useRef } from 'react';

// Create chat context
const ChatContext = createContext();

function ChatProvider({ children }) 
{
    // Global states
    const [selectedUser, setSelectedUser] = useState(null);
    const [selectedGroup, setSelectedGroup] = useState(null);
    const [privateMessages, setPrivateMessages] = useState([]);
    const [groupMessages, setGroupMessages] = useState([]);
    const [showModal, setShowModal] = useState(false);

    // Global references
    const privateConversationRef = useRef(null);
    const groupConversationRef = useRef(null);

    return (
        <ChatContext.Provider value={{ selectedUser, setSelectedUser, selectedGroup, setSelectedGroup, 
        privateMessages, setPrivateMessages, groupMessages, setGroupMessages, showModal, setShowModal, 
        privateConversationRef, groupConversationRef }}>
            { children }
        </ChatContext.Provider>
    );
}

// Custom hook
export const useChat = () => useContext(ChatContext);

export default ChatProvider;
```

---

**src/utils/generateConversationId.js**
```javascript
const generateConversationId = (senderId, receiverId) => {
    return [senderId.toString(), receiverId.toString()].sort().join("-");
};

export default generateConversationId;
```

**src/components/PrivateChats.jsx**
```javascript
import { useCallback, useEffect, useState, useRef } from "react";
import styles from "./style.module.css";
import { useChat } from "../../context/chat";
import useSocket from "../../hooks/useSocket";
import axios from "axios";
import generateConversationId from "../../utils/generateConversationId";

function PrivateChats() 
{
    // Global States
    const { selectedUser, privateMessages, setPrivateMessages, privateConversationRef } = useChat();
    const [error, setError] = useState("");

    // Fetch chats
    useEffect(() => {
        if(!selectedUser?._id) return;
        privateConversationRef.current = generateConversationId(userData?._id, selectedUser?._id);
        setPrivateMessages([]);
        setError("");

        axios.get(`/chat/private-message/${selectedUser?._id}`);
        .then((response) => setPrivateMessages(response.data))
        .catch((error) => setError(error.message));
    }, [selectedUser?._id]); 

    // Listen for incoming messages in real time
    useSocket("private-message", useCallback((data) => {
        // Only accept messages if they belong to the current chat
        if(data.conversationId === privateConversationRef.current)
        {
            setPrivateMessages((prev) => [...prev, data]);
        }
        else
        {
            // Message appear on chat list as a message badge
        }
    }, []));

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
import { useCallback, useEffect, useState, useRef } from "react";
import styles from "./style.module.css";
import { useChat } from "../../context/chat";
import useSocket from "../../hooks/useSocket";
import axios from "axios";
import generateConversationId from "../../utils/generateConversationId";

function GroupChats() 
{
    // Global States
    const { selectedGroup, groupMessages, setGroupMessages, groupConversationRef } = useChat();
    const [error, setError] = useState("");

    // Fetch chats
    useEffect(() => {
        if(!selectedGroup?._id) return;
        groupConversationRef.current = generateConversationId(userData?._id, selectedUser?._id);
        setGroupMessages([]);
        setError("");

        axios.get(`/chat/group-message/${selectedGroup?._id}`)
        .then((response) => setGroupMessages(response.data))
        .catch((error) => setError(error.message));
    }, [selectedGroup?._id]); 

    // Listen for incoming messages in real time
    useSocket("group-message", useCallback((data) => {
        // Accept only currect selected group mesages
        if(data.conversationId === groupConversationRef.current) 
        {
            setGroupMessages((prev) => [...prev, data]);
        }
        else
        {
            // Message appear on chat list as a message badge
        }
    }, []));

    return(
        <>
            {/* UI Design Here */}            
        </>
    );
}

export default GroupChats;
```