# 🚀 Connecting Server to DB

## 📌 Thank the Developer Community

1. As we have learned, organizations will make separate technologies for specific functions but often they will leave it up to the wider tech community to bridge the gaps. In this case we have to bridge the gap between our PostgreSQL database and our Node/Express.js server. To do that we will use the appropriately named node package "node-postgres". Run `npm install pg` in the terminal. Make sure you are in the "backend" directory.

2. You may ask yourself, "how would I know to do this if you didn't tell me"? The answer is basically Google it and hope someone made a package and that there is good documentation or a youtube tutorial for it. That is actually a professional answer.

## 📌 Setting up our Environment

1. Now we configure our environment in preparation to connect our PostgreSQL database with our node & express.js server

   - Open your .env file and create five new variables: PGUSER, PGPASSWORD, PGHOST, PGPORT, and PGDATABASE. You should know all of this information if you look at your .env file from the yelp clone tutorial. I recommend saving it in your notes app as well.

   - The one piece of information that we are going to need to change is PGDATABASE. You should have already created your db as a part of the software requirements engineering phase. So go ahead and make the value of PGDATABASE the name of that database.

   - Double check the port of your database as well. Google it if you don't know how to do that.

2. Next we want to update our file structure. In the backend directory we are going to make two new folders called "db" and "csvs". Inside of "db" we are going to make a file called "index.js". Inside of "csvs" we are going to add our csv file for our data. You will not always want to make your csv available in github. You don't need to for the database to work. We are just doing it so that people can see the csv file that you used for your project.

## 📌 Connecting through Code

1. Node-postgres is a good example of providing helpful and clear documentation ([https://node-postgres.com/guides/project-structure](https://node-postgres.com/guides/project-structure)) but it guides you to implement more complex functionality than we need at the moment. We will learn about the more complex implementation next term. For now you can just copy paste the code from this file in "index.js". Be sure to read the comments to understand what the code is doing!

   ```javascript
   // First, we're importing a helpful tool called 'pg' (think of it as a Swiss Army knife for working with PostgreSQL databases)
   import pg from "pg";
   const { Pool } = pg;

   // We're creating a 'pool' of database connections
   // Think of this like a group of friendly assistants ready to help us talk to the database
   // It's much faster than hiring a new assistant every time we need something!
   // Note: Pool automaticaly finds our environment variables in .env to establish the connection
   const pool = new Pool();

   // Here's where we create our own special way to ask questions to the database
   // It's like setting up a telephone hotline for database queries
   export const query = (text, params, callback) => {
     // When someone calls our hotline (query function), we pass their question to one of our assistants (pool)
     // text: the question we're asking (in SQL language)
     // params: any specific details for the question
     // callback: what to do when we get an answer
     console.log("Hello from your yelp db!");
     return pool.query(text, params, callback);
   };
   ```

2. Now we need to import index.js in our server.js file so that our server can call the index.js' query function in response to requests. Add this import to the top of server.js, `import * as db from "./db/index.js";`

   ```javascript
   // We use express to create our servers endpoints, and listen for & respond to requests from the frontend
   import express from "express";
   // We use dotenv so that we can access our environment variables
   import "dotenv/config";
   // We import our index.js so that we can query our database
   import * as db from "./db/index.js";
   ```

3. Now we want to test our server-database connection

   - First I recommend we setup our terminal. In VSCode you can have multiple terminals at once (Many of you create dozens of them on accident instead of just hitting "^c"). We want to open two. One running our psql db and another for our node/express.js server. You can even right click to rename them (you can also split screen them if you want). Make sure to do this before moving onto the next step.

   - Let's go back to our "get-template" endpoint. We are going to update this to be descriptive of the information that we are getting from our db. For example, if I were working with air quality data I would change it from "/api/v1/get-template" to "/api/v1/air-quality-data". Be sure to change it in postman too.

   - Next we have to update our callback function to query the database and send back a response. You can view and copy the example in my server.js file (link: https://github.com/CoachAsim/east-village-aq-analyzer-epfp2/blob/main/backend/server.js)
   
   ```javascript
   // Get air quality data
   requestHandler.get("/api/v1/air-quality-data", async (req, res) => {
     const dbResponse = await db.query("select * from air_quality limit 5");
     console.log(dbResponse);
     res.send(dbResponse);
   });
   ```

     ### 🔹 To understand the code is doing, you must understand async/await in javascript

     - When a client makes a request to this endpoint, the server needs to query the database to fetch air quality data. Database operations take time to complete, as they involve disk access and potentially network communication. Without async/await, the server would freeze while waiting for the database query to finish, blocking all other incoming requests. By marking the route handler as async and using await before the database query, we're telling JavaScript to:
       - Continue handling other tasks while waiting for the database query to complete
       - Pause execution of the specific route handler at the await line
       - Resume the handler only when the database returns results

     - This non-blocking approach allows the server to efficiently handle multiple requests simultaneously, even when some require time-consuming database operations. In this specific example, when the server receives a request for air quality data:
       - The handler starts executing
       - When it reaches await db.query(...), it pauses that specific handler
       - The server remains responsive to other requests during this time
       - Once the database returns the air quality records, execution resumes
       - The results are logged to the console and sent to the client

     - Without async/await, the server would appear unresponsive until each database query completed, significantly reducing performance and user experience.

     - Once you have updated your route's callback function, press send in postman. You should see a response from our server in postman (the client) and you should see a console log of the database query response in the terminal.

> 🎯 **Success!** We have connected our node/express.js server to our psql database and it can respond to client requests with our data (hint: the response is in the form of an object. Make sure you know how js objects work).

Next we have to make the frontend that users will interact with on their devices. Those devices are the clients that will make requests to our server (the backend).
