# 🔌 Connecting Your React Frontend to Your Express Backend

## 🚀 Overview

Awesome job setting up your Express server and connecting it to PostgreSQL! Now comes the exciting part - connecting your React frontend to your backend so they can communicate with each other!

Think of your app like this:
- 🎨 **Frontend (React)** - What users see and interact with
- 🔄 **Backend (Express/Node.js)** - Processes requests and talks to the database
- 🗄️ **Database (PostgreSQL)** - Stores all your important data

## 🤔 The Problem

When you're developing, your React app and Express server run on **different ports**:
- 🎨 React typically runs on: `http://localhost:3000`
- 🔄 Express typically runs on: `http://localhost:3001` (or whatever port you chose)

This creates two challenges:
1. How does your React app know where to find your Express server?
2. How do we avoid CORS errors? This is a big one we need to understand!

## 🛡️ CORS: The Internet's Bouncer

### What is CORS? 🤷‍♀️

CORS (Cross-Origin Resource Sharing) is like the security guard or bouncer at a club. Imagine your website is an exclusive party, and CORS is checking everyone's ID at the door.

In web terms, CORS is a security feature built into browsers that blocks your frontend JavaScript from talking to APIs on different domains/ports/protocols than the one your website came from.

### Why was CORS created? ⚠️

Before CORS existed, any website could make requests to any other website's API in the background without you knowing! Imagine if Instagram could secretly access your Venmo API when you visited their site - that's scary!

CORS was created to protect users from these kinds of attacks (called "Cross-Site Request Forgery" or CSRF). It's like putting locks on all the doors between websites.

### How CORS works in practice 🔍

When your React app (on `localhost:3000`) tries to fetch data from your Express server (on `localhost:3001`), here's what happens:

1. Your browser sends the request to your Express server
2. It also sends an `Origin` header saying "I'm coming from localhost:3000"

### What are "headers" anyway? 📝

HTTP headers are like the extra information attached to messages sent between your browser and servers. Think of them like the details on a mailing envelope:

- The content of your letter is your data (JSON, HTML, etc.)
- Headers are all the other information on the envelope (sender address, recipient, priority, etc.)

Headers contain important metadata about the request or response, such as:
- What type of content is being sent
- Authentication information
- Where the request is coming from
- What the browser can accept

You can see headers in the "Network" tab of your browser's developer tools, or in Postman under the "Headers" section of a request/response.

### "But wait, I haven't used headers in my Express code!" 🤔

Looking at your Express routes, you might be thinking: "I never set any headers in my code!"

```javascript
// GET template
requestHandler.get("/api/v1/get-template", (req, res) => {
    res.send("Hello World!");
});

// Get air quality data
requestHandler.get("/api/v1/air-quality-data", async (req, res) => {
    const dbResponse = await db.query("select * from air_quality limit 5");
    console.log(dbResponse);
    res.send(dbResponse);
});
```

You're right! There are two reasons you haven't had to deal with headers directly:

1. **Express handles many headers automatically** - When you use `res.send()`, Express automatically sets headers like `Content-Type` for you!

2. **Testing with Postman bypasses CORS** - Postman is a special tool that doesn't enforce CORS restrictions like browsers do, so you never saw CORS errors there.

When browsers make requests, they're much stricter than Postman about security! That's why you'll need to handle CORS when your React app talks to your Express server.

Back to our CORS situation:

3. Your Express server needs to respond with a special header:
   ```
   Access-Control-Allow-Origin: http://localhost:3000
   ```
   This is like a permission slip saying "Yes, I'll allow requests from this origin"
   
4. If that permission header is missing, your browser blocks the response, and you get the dreaded CORS error!

### CORS: The Developer's Nemesis 😩

Ask any web developer about CORS, and they'll probably groan or laugh. It's one of the most common roadblocks developers face. CORS errors are so common there are endless memes about them - it's practically a developer rite of passage!

CORS is like that strict teacher who won't accept late homework - annoying when you're facing it, but ultimately there for a good reason.

### Development vs. Production CORS 🌐

First, what are "development" and "production"? 🤔

**Development environment** is when you're building the app on your computer:
- Only you can see and use it
- You're constantly making changes and fixing bugs
- Everything runs locally (localhost)
- You have special tools to help with debugging

**Production environment** is when your app is deployed for real users:
- It's on a server somewhere on the internet
- Real people are using it
- Changes go through testing before being deployed
- Performance and security are top priorities

Now, about CORS in these environments:

In development, CORS is a pain because:
- You're usually running separate servers (React on one port, Express on another)
- You're the only one using the app, so security is less critical
- You're frequently refreshing and testing different features

In production, CORS is often less of an issue because:
- You typically serve your frontend and API from the same domain
- When you do need cross-origin requests, you've properly configured CORS headers
- Everything is set up once and runs consistently

### How we overcome CORS 💪

There are several ways to handle CORS:

1. **The proxy approach** (what we'll use) - React's development server forwards API requests
2. **CORS middleware** - Add headers on your Express server using `cors` package
3. **Same-origin deployment** - Serve frontend and backend from the same origin in production

For learning purposes, the proxy approach is easiest because it requires no extra configuration on your backend.

## 🛠️ The Solution: Proxy Configuration

### What is a Proxy? 🤔

Before we dive into the solution, let's understand what a "proxy" actually is:

A **proxy** is like a middleman or go-between that forwards requests from one place to another. Think of it like:

- 📬 A mail forwarding service that takes mail from one address and sends it to another
- 🗣️ A translator who helps two people who speak different languages communicate
- 🚧 A traffic controller who redirects cars to avoid construction zones

In web development, a proxy server sits between a client (like your React app) and a server (like your Express backend). Instead of the client talking directly to the server, it talks to the proxy, which then communicates with the server on its behalf.

### Why Use a Proxy? 🎯

In our development setup, the React proxy serves several important purposes:

1. **Avoids CORS issues** - Since requests appear to come from the same origin
2. **Simplifies URLs** - You can use relative paths instead of full URLs
3. **Hides server details** - Your React app doesn't need to know exactly where the backend is
4. **Reduces complexity** - No need for extra CORS configuration on your Express server

Now let's see how to implement this solution.

### 👉 Step 1: Add a Proxy to your Frontend

Open the `package.json` file in your **frontend** directory and add this line at the root level of the JSON object:

```json
"proxy": "http://localhost:3000"
```

⚠️ **IMPORTANT**: Replace `3000` with whatever port your Express server is running on! (Check your .env file in the backend folder for the PORT value)

For example, if your Express server is running on port 5000, you would write:

```json
"proxy": "http://localhost:5000"
```

### 👉 Step 2: Preparing for API Requests from React

Now that your proxy is set up, your React app will be able to communicate with your Express server. This means when you learn how to make API requests from React (which you'll cover soon!), you'll be able to access your endpoints without running into CORS issues.

For example, if you have an endpoint `/api/v1/air-quality-data` that you've tested in Postman, the proxy will allow your React code to access it without needing to specify the full URL with hostname and port.

Why does this matter? Because without the proxy:
- You'd need to use the full URL: `http://localhost:3000/api/v1/air-quality-data`
- You'd run into CORS errors we discussed earlier
- You'd need extra backend configuration

With the proxy set up, your future React code will be simpler and work right away!

You'll learn the specific React code for making these requests in your upcoming lessons, so don't worry about the implementation details just yet.

## 🧪 Testing Your Connection

### The Simplest Proxy Test

Here's the absolute simplest way to test if your proxy is set up correctly, using Postman (which you're already familiar with):

1. Make sure your Express server is running (e.g., on port 3001)
2. Make sure your React app is running (typically on port 3000)
3. Open Postman
4. Create a GET request to your API endpoint - but use your React app's port instead of your Express server's port!

   For example: `http://localhost:3000/api/v1/air-quality-data`
   
   (Notice we're using port 3000 - the React app's port - not the Express port)

If your proxy is working correctly, Postman will show the same response as if you had directly called your Express endpoint at port 3001! The React development server is forwarding your request behind the scenes.

If you see an error or no response:
- Double-check your proxy setting in package.json
- Ensure both servers are running
- Verify the endpoint URL is exactly right

This test confirms that your proxy configuration is correctly forwarding API requests without you having to write any React code yet.

## 🚧 Troubleshooting

If your proxy test doesn't work:

1. **Check both servers are running** - Look at your terminal windows
2. **Verify the proxy port** - Make sure it matches your Express server port
3. **Look for CORS errors** - Check your browser's developer console
4. **Check your API endpoint** - Make sure the path is exactly right (case-sensitive)
5. **Check your Express route** - Make sure it's sending back data properly

> 🎯 **Success!** We have our frontend connected to our backend! Now we are technically setup to begin our frontend development. But first we want to make sure we understand the core react concepts. That's what we will do next!
