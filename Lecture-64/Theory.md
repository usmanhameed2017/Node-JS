# 🅿️ PayPal Payment Gateway Integration

**PayPal** is a globally recognized online payment platform that allows individuals and businesses to send and receive money securely over the internet.
It acts as a **digital wallet** where users can link their bank accounts, debit, or credit cards and make transactions without sharing sensitive financial details directly with merchants.

PayPal supports multiple currencies and provides developers with APIs and SDKs to integrate secure **checkout systems** into websites and mobile apps.

This makes it one of the most trusted and convenient payment gateways for e-commerce and subscription-based platforms.

### 🛠 Implementation

#### Step:01 API KEYS

Get your test or live **API Keys** from PayPal dashboard.

#### ⚙ Step:02 Installation

Install the **PayPal** official package.
```bash
npm install "@paypal/checkout-server-sdk" 
```

#### 🔐 Step:03 Environment Variables

Setup your environment variables.
```javascript
PAYPAL_CLIENT_ID="YourPayPalClientID"
PAYPAL_CLIENT_SECRET="YourPayPalClientSecret"
```

---

#### 🧠 Codebase Integration
```javascript
const express = require("express");
const ApiError = require("./utils/ApiError");
const ApiResponse = require("./utils/ApiResponse");
const User = require("./models/user");
const paypal = require('@paypal/checkout-server-sdk');
const { isProduction } = require("../constants");

// Initialize Express App
const app = express();

/************************ Middlewares ************************/
app.use(express.urlencoded({ extended: true, limit: "100kb" }));
app.use(express.json({ limit: "100kb" }));

/*********************** API ENDPOINTS ***********************/
// PayPal environment setup
const environment = isProduction
? new paypal.core.LiveEnvironment(process.env.PAYPAL_CLIENT_ID, process.env.PAYPAL_CLIENT_SECRET)
: new paypal.core.SandboxEnvironment(process.env.PAYPAL_CLIENT_ID, process.env.PAYPAL_CLIENT_SECRET);

// PayPal client
const paypalClient = new paypal.core.PayPalHttpClient(environment);

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

        // Create order request
        const orderRequest = new paypal.orders.OrdersCreateRequest();
        orderRequest.requestBody({
            intent: "CAPTURE",
            purchase_units: [{
                amount: {
                    currency_code: "USD",
                    value: Number(amount).toFixed(2),
                },
                description: `${plan} Plan`,
                custom_id: user._id.toString(),
            }],
            application_context: {
                brand_name: "BSK-TV",
                return_url: `${process.env.BACKEND_URL}/api/v1/payment/paypal/capture-order?_id=${_id}&plan=${plan}`,
                cancel_url: `${process.env.BACKEND_URL}/api/v1/payment/paypal/cancel`,
            },
        });

        // Execute order request
        const order = await paypalClient.execute(orderRequest);

        // Get approval link
        const approvalLink = order?.result?.links?.find(link => link?.rel === "approve")?.href;

        // Response
        return response.status(200).json(new ApiResponse(200, { approvalLink, orderId:order.result.id }, "PayPal order created"));
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
        // Get token from query string
        const { token, _id, plan } = request.query;
        if(!token || !_id || !plan) throw new ApiError(400, "Missing required fields");

        // Create capture request
        const captureRequest = new paypal.orders.OrdersCaptureRequest(token);
        captureRequest.requestBody({});

        // Execute capture request
        const capture = await paypalClient.execute(captureRequest);

        if(capture.result.status === "COMPLETED") 
        {
            // Get subscription dates
            const startDate = new Date();
            const endDate = new Date();
            endDate.setMonth(endDate.getMonth() + (plan === "Yearly" ? 12 : 1));

            // Prepare payload
            const payload = { gateway:"paypal", subscriptionId:token, plan, startDate, endDate, isActive:true, status:"active" };

            // Update in DB
            await User.findByIdAndUpdate(_id, { subscription:payload }, { new:true });

            // Response
            return response.status(200).json(new ApiResponse(200, null, "Payment captured & subscription activated"));
        } 
        else 
        {
            throw new ApiError(400, "Payment not completed");
        }
    } 
    catch (error) 
    {
        throw error;
    }
});

// PayPal cancel payment
app.get("/api/v1/payment/paypal/cancel", (request, response) => {
    throw new ApiError(400, "PayPal payment failed");
});
```