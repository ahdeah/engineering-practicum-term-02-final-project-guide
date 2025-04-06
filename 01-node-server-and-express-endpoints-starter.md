# 🚀 Node Server & Express Endpoints Starter

## 📌 Backend

1. Create a github repository, initialize git in your local directory, and then connect the two.
2. Create a directory inside of your project directory called "backend".

## 📌 Node Setup

1. In the terminal navigate to your "backend" directory and run `npm init`. This command initializes (init is short for initialize) your backend node environment by creating a package.json file which contains data about your app and manages the node packages that your app depends on (aka your apps dependencies).

   - Fill in the details that it asks for in the terminal
     - You can leave the following empty or as the default by just pressing the return key: package name, version, test command, keywords, license
     - Set entry point to "server.js"
   
   - On a new line, add this property-value pair to package.json after the "main" property-value pair: `"type": "module"`. Don't forget to add a comma after. This specifies the way that we import modules aka other files that provide code that we need.
   
   ```json
   {
     "name": "backend",
     "version": "1.0.0",
     "description": "backend of AQ analyzer app",
     "main": "server.js",
     "type": "module",
     "scripts": {
       "start": "nodemon server.js",
       "test": "echo \"Error: no test specified\" && exit 1"
     },
     // other properties...
   }
   ```

2. Create your backend entry point by creating a file called "server.js"

3. Create an environment variable file called ".env". This file contains the variables that change depending on the environment you are working in. For now you are only working in your local environment aka your computer. But eventually we will make our apps available to everyone by hosting them on servers run by companies.

4. Stage and commit your changes to git.

> 📝 **Note:** Now that we have our node environment setup, we need to install express which we use to create our endpoints, start our server, and listen for & respond to requests from the frontend.

## 📌 Express Setup

1. Install express by running the `npm install express` command in the terminal. You'll notice your package.json file now has an "M" to the right of it in your file explorer. That is because express was added to the dependencies that package.json manages.

2. Inside of server.js, import express by writing `import express from "express"`

3. We need to create an express app by calling the `express()` function and storing it into a variable called "requestHandler". Usually this variable is called "app", but I think "requestHandler" better illustrates how we use express to create endpoints, listen for & respond to requests.

4. When our requestHandler handles requests it is often parsing json to understand what was requested. To do that we need to use some "middleware". Middleware is code that processes a request before it gets to the requested api endpoint. We need middleware that will parse json data and make it available to us as an object data type. Add this line to your server.js file after you've created the express instance. `requestHandler.use(express.json());`

   ```javascript
   // We use express to create our servers endpoints, and listen for & respond to requests from the frontend
   import express from "express";
   // We use dotenv so that we can access our environment variables
   import "dotenv/config";
   
   // Create an instance of express
   const requestHandler = express();
   // Storing our port value from the .env file
   const port = process.env.PORT;

   // The middleware express.json() parses incoming JSON requests and puts the parsed data in req.body
   requestHandler.use(express.json());
   ```

5. We will use our express request handler to start and listen to our server. But to do that we need to define where the request handler should be listening. We do this by selecting a port. In your ".env" file create the variable `PORT=3000`.

   ### 🔹 Ports Explained: WHAT IS A PORT?

   Think of your computer like a house:

   - The IP address is like your house address (which building/computer)
   - Port numbers are like different doors to your house, each for a specific purpose:
     - Front door (Port 80/443) = Where web visitors normally enter
     - Side door (Port 3000) = Where our app accepts visitors
     - Garage door (Port 21) = For moving large files in/out
     - Secure back entrance (Port 22) = Only for authorized maintenance
     - Each service running on your computer needs its own "door" (port) to avoid conflicts.
     
   #### 💡 WHY PORT 3000?
   
   We chose port 3000 for our app, but we could have chosen many other available ports. Common development ports include 3000, 3001, 3005, 8000, 8080, etc. Ports 0-1023 are reserved for system services, so we avoid those.

6. To use our environment PORT variable we need a package called "dotenv". It allows us to access our environment variables in our javascript files. Install "dotenv" by running the `npm i dotenv` command. Then import it into server.js by adding the line `import "dotenv/config"`

7. Now we can create a variable called "port" that stores the value of the property accessed at "process.env.PORT"

8. Add a line to log the value of the port variable and run your server.js file by using the `npm start` command in terminal. You should see "3000" in the terminal.

9. Now we can start our http server and listen at our specified port by calling our express instance's "listen()" function.

   ```javascript
   // Starting our http server and listening for requests!
   requestHandler.listen(port, () => {
     console.log(`Server listening on port ${port}`);
   });
   ```

10. Now that we can start our server, we want it to be able to respond to changes as we are developing so that we don't have to stop and start it every time we make a change. To do this we will install the "nodemon" package using the `npm i nodemon` command. Next we have to add a script to our package.json. `"start": "nodemon server.js"`. Next time you run `npm start` you should see "[nodemon]..." in the terminal

   ```json
   "scripts": {
     "start": "nodemon server.js",
     "test": "echo \"Error: no test specified\" && exit 1"
   }
   ```

11. Now is a good time to stage and commit your code! Make sure your commit message is descriptive of what you did. For now, It shouldn't be longer than a sentence though : )

> 📝 **Note:** Now that we have setup and started our server, we can create some template endpoints for our CRUD operations between the app and our database. We won't fully create them now. Each of you will have your own specific needs. But we will create a template for each type of RESTful HTTP method.

> 🚨 **IMPORTANT:** To meet the minimum requirements your app only needs to use the get http method to read from the database. If you complete the minimum requirements you can add functionality to create, update and delete from the database! But only after meeting the minimum requirements.

## 📌 Express Routing

1. Let's start with a template route that responds to get requests.

   ```javascript
   // GET template
   requestHandler.get("/api/v1/get-template", (req, res) => {
     res.send("Hello World!");
   });
   ```

   ### 🔹 Lets breakdown this code step by step

   #### 💡 Step A: The Request Handler
   - requestHandler is our Express application object. Think of it as the main switchboard for our website that directs traffic to the right place.

   #### 💡 Step B: The HTTP Method
   - .get specifies that this route responds to GET requests only. GET is one of several HTTP methods:
     - GET: Used when retrieving information (like loading a webpage)
     - POST: Used when submitting information (like a form)
     - PUT/PATCH: Used when updating information
     - DELETE: Used when removing information

   #### 💡 Step C: The Route Path
   - "/api/v1/get-template" is the URL path where this route will be active:
     - /api indicates this is part of your application's API
     - /v1 indicates this is version 1 of your API
     - /get-template is the specific resource or action being requested

   #### 💡 Step D: The Callback Function
   - (req, res) => { ... } is a function that runs when someone visits this route:
     - It takes two parameters: req and res
     - It's written using "arrow function" syntax (a modern JavaScript feature)

   #### 💡 Step E: The Request Object
   - req contains all information about the incoming request:
     - Who's making the request
     - Any data they've sent
     - Headers, cookies, and other request information

   #### 💡 Step F: The Response Object
   - res provides methods to send information back to the user:
     - It lets you control what the user receives

   #### 💡 Step G: The Response Action
   - res.send("Hello World!"); sends a text response back to the user:
     - When they visit "/api/v1/get-template", they'll see "Hello World!" displayed

   #### 💡 The Flow in Action (What Actually Happens)
   - A user types "www.yourwebsite.com/api/v1/get-template" in their browser
   - The browser sends a GET request to your server
   - Express receives this request and looks for a matching route
   - It finds your route definition that matches "/api/v1/get-template"
   - It calls your callback function, providing request and response objects
   - Your code executes res.send("Hello World!")
   - The server sends "Hello World!" back to the user's browser to be processed and/or displayed on the screen

2. Now that we've created the template route and understand what it does, let's test it. Open the Postman app and create a new collection of requests. Call it "winter-final-project".

   - Inside of that collection create a new request called "get-template"
     - Set the url to "http://localhost:3000/api/v1/get-template". If your port is different than 3000 then you'll need to change this to that number. Double check! Also double check your terminal to make sure your server is running

3. Click the send button to the right of the request URL. You should see "Hello World!" in the response body!

> 🎯 **Success!** We have our Node & Express JS server responding to a simple get request. Next we should get our database setup so we can have our GET requests respond with our data from the database!
