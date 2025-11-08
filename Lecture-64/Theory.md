# 🅿️ PayPal Payment Gateway Integration

**PayPal** is a globally recognized online payment platform that allows individuals and businesses to send and receive money securely over the internet.
It acts as a **digital wallet** where users can link their bank accounts, debit, or credit cards and make transactions without sharing sensitive financial details directly with merchants.

PayPal supports multiple currencies and provides developers with APIs and SDKs to integrate secure **checkout systems** into websites and mobile apps.

This makes it one of the most trusted and convenient payment gateways for e-commerce and subscription-based platforms.

### 🛠 Implementation

#### Step:01 API KEYS

Get your test or live **API Keys** from PayPal dashboard.

#### ⚙ Step:02 Installation

Install the **PayPal** package.
```bash
npm install "@paypal/paypal-server-sdk"
```

---

#### 🧠 Codebase Integration
```javascript
const express = require("express");
const ApiError = require("./utils/ApiError");
const ApiResponse = require("./utils/ApiResponse");
const User = require("./models/user");
const { Client, Environment } = require("@paypal/paypal-server-sdk");
const { isProduction } = require("../constants");

// Initialize Express App
const app = express();

/************************ Middlewares ************************/
app.use(express.urlencoded({ extended: true, limit: "100kb" }));
app.use(express.json({ limit: "100kb" }));

/*********************** API ENDPOINTS ***********************/
// PayPal client configuration
const paypalClient = new Client({
    clientCredentialsAuthCredentials: {
        oAuthClientId: process.env.PAYPAL_CLIENT_ID,
        oAuthClientSecret: process.env.PAYPAL_CLIENT_SECRET
    },
    environment: isProduction ? Environment.Production : Environment.Sandbox,
});

// Create PayPal Order
app.post("/api/v1/payment/paypal", (request, response) => {
    try 
    {
        // Get data from frontend
        const { _id, plan, amount } = request.body;

        // Sanitize data
        if(!_id) throw new ApiError(404, "ID is required");
        if(!plan) throw new ApiError(404, "Payment plan is required");
        if(!amount || amount <= 0) throw new ApiError(404, "Amount is required");

        // Validate user
        const user = await User.findById(_id).select("_id").lean();
        if(!user) throw new ApiError(404, "User not found! Invalid ID");

        // Build order request
        const order = await paypalClient.orders.create({
            body: {
                intent: "CAPTURE",
                purchase_units: [
                    {
                        amount: {
                            currency_code: "USD",
                            value: amount.toFixed(2),
                        },
                        description: `${plan} Plan`,
                        custom_id: user._id.toString(),
                    },
                ],
                application_context: {
                    brand_name: "BSK-TV",
                    return_url: `${process.env.BACKEND_URL}/api/v1/payment/paypal/capture-order?_id=${_id}&plan=${plan}`,
                    cancel_url: `${process.env.BACKEND_URL}/api/v1/payment/paypal/cancel`
                },
            },
        });
        
        // Respond with approval link
        const approvalLink = order?.result?.links?.find(link => link.rel === "approve")?.href;
        return response.status(200).json(new ApiResponse(200, { approvalLink, orderId:order?.result?.id }, "PayPal order created"));        
    } 
    catch(error) 
    {
        throw error;
    }
});

// Capture PayPal Order
app.get("/api/v1/payment/paypal/capture-order", (request, response) => {
    try 
    {
        const { token, _id, plan } = request.query;

        // Token is the PayPal order id
        if(!token || !_id || !plan) throw new ApiError(400, "Missing required fields");
    
        // Capture order request
        const capture = await paypalClient.orders.capture(token);
    
        if(capture.result.status === "COMPLETED") 
        {
            // Get subscription dates
            const startDate = new Date();
            const endDate = new Date();
            endDate.setMonth(endDate.getMonth() + (plan === "Yearly" ? 12 : 1));
    
            // Prepare payload
            const payload = { gateway:"paypal", subscriptionId:token, plan, startDate, endDate, isActive:true, status:"active" };
            await User.findByIdAndUpdate(_id, { subscription:payload }, { new: true });
    
            // Response
            return response.status(200).json(new ApiResponse(200, null, "Payment captured & subscription activated"));
        } 
        else 
        {
            throw new ApiError(400, "Payment not completed");
        }
    } 
    catch(error) 
    {
        throw error;
    }
});

// PayPal cancel payment
app.get("/api/v1/payment/paypal/cancel", (request, response) => {
    throw new ApiError(400, "PayPal payment failed");
});
```