# 💳 STRIPE PAYMENT GATEWAY INTEGRATION

**Stripe** is a powerful and developer-friendly payment processing platform that enables businesses to securely accept online payments. It supports multiple payment methods, including credit/debit cards, digital wallets (like Apple Pay and Google Pay), and various local payment options.

With Stripe’s robust APIs and libraries, developers can easily build custom checkout flows, manage subscriptions, and handle transactions efficiently. Its advanced dashboard, real-time analytics, and fraud prevention tools make it one of the most reliable solutions for modern web applications.

### 🛠 Implementation

#### Step:01 API KEYS

Get your test or live **API Keys** from stripe dashboard.

#### ⚙ Step:02 Installation

Install the **stripe** package.
```bash
npm install stripe
```

#### 🔐 Step:03 Environment Variables

Setup your environment variables.
```javascript
STRIPE_PUBLISHABLE_KEY="YourStripePublishableKey"
STRIPE_SECRET_KEY="YourStripeSecretKey"
```

---

#### 🧠 Codebase Integration
```javascript
const express = require("express");
const ApiError = require("./utils/ApiError");
const ApiResponse = require("./utils/ApiResponse");
const User = require("./models/user");
const Stripe = require("stripe");

// Initialize Express App
const app = express();

/************************ Middlewares ************************/
app.use(express.urlencoded({ extended: true, limit: "100kb" }));
app.use(express.json({ limit: "100kb" }));

/*********************** API ENDPOINTS ***********************/
// Stripe session creation
app.post("/api/v1/payment/stripe", (request, response) => {
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

        // Stripe instance
        const stripe = new Stripe(process.env.STRIPE_SECRET_KEY);

        // Create session
        const session = await stripe.checkout.sessions.create({
            payment_method_types: ["card"],
            mode: "payment",
            line_items: [{
                price_data: {
                    currency: "usd",
                    unit_amount: amount * 100,
                    product_data: { 
                        name: `${plan} Plan`,
                        description: "BSK-TV Streaming Platform – Enjoy ad-free HD entertainment.",
                        images: ["https://ik.imagekit.io/usman2017/bskTvLogo.png"],
                        metadata:{
                            brand: "BSK-TV",
                            category: "Streaming Subscription"
                        }
                    },
                },
                quantity: 1,
            }],
            metadata: { _id, plan },
            success_url: `${process.env.BACKEND_URL}/api/v1/payment/stripe/success?session_id={CHECKOUT_SESSION_ID}`,
            cancel_url: `${process.env.BACKEND_URL}/api/v1/payment/stripe/cancel`
        });

        // Validate
        if(!session) throw new ApiError(400, "Stripe session creation failed");

        // Response
        return response.status(200).json(new ApiResponse(200, session.url, "Checkout url generated"));
        /*
            Note: You can also redirect users directly from server
            return response.status(303).redirect(session.url);
        */
    } 
    catch (error) 
    {
        throw error;
    }
});

// Stripe payment verification
app.get("/api/v1/payment/stripe/success", (request, response) => {
    try 
    {
        // Validate session ID
        const { session_id } = request.query;
        if(!session_id) throw new ApiError(400, "Session ID is missing");

        // Stripe instance
        const stripe = new Stripe(process.env.STRIPE_SECRET_KEY);

        // Get checkout session details
        const session = await stripe.checkout.sessions.retrieve(session_id);

        // Validate session
        if(!session) throw new ApiError(404, "Session not found");

        // Check payment status
        if(session.payment_status === "paid") 
        {
            // Get metadata
            const { _id, plan } = session.metadata;

            // Get subscription dates
            const startDate = new Date();
            const endDate = new Date();
            endDate.setMonth(endDate.getMonth() + (plan === "Yearly" ? 12 : 1));

            // Prepare payload
            const payload = { gateway:"stripe", subscriptionId:session.id, plan, startDate, endDate, isActive:true, status:"active" };
            
            // Create subscription
            const user = await User.findByIdAndUpdate(_id, { subscription:payload }, { new:true });
            if(!user) throw new ApiError(400, "Failed to create subscription in DB");
            return response.status(200).json(new ApiResponse(200, null, "Payment verified & subscription activated"));
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

// Stripe payment cancellation
app.get("/api/v1/payment/stripe/cancel", (request, response) => {
    throw new ApiError(400, "Stripe payment failed");
});
```