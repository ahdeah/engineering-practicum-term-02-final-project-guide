# 🚀 Setting up our Frontend: Create React App

## 📌 Introduction

We have connected our Node/Express.js server to our PostgreSQL database, and it can respond to client requests with our data (hint: the response is in the form of an object. Make sure you know how JavaScript objects work).

Now we need to set up our frontend by creating a React application. Before we proceed, I want you to read the homepage of React's documentation from top to bottom. It's one of the best overviews of a web development library you'll find.

> 🚨 **IMPORTANT:** [READ THE REACT DOCUMENTATION](https://react.dev/) BEFORE MOVING FORWARD (Make sure to hover over the code snippets).

## 📌 Create React App

To create our React app, we are going to use the "create-react-app" tool provided by the React team. 
[https://create-react-app.dev/docs/getting-started/](https://create-react-app.dev/docs/getting-started/)

To do this, we will be using a new way to interact with node packages: the terminal command "npx".

### 🔹 NPM vs NPX

- **Installation vs. Execution:** npm installs packages for ongoing use, while npx executes packages temporarily without installing them.

- **Version Management:** npx ensures you're using the latest version of a package, whereas with npm, you need to manually update installed packages.

- **Disk Space (storage):** npx uses less disk space since it doesn't store packages locally after execution.

> 💡 **Tip:** In the case of create-react-app, using npx is beneficial because it ensures you're always setting up your project with the latest version of the tool, without cluttering your system with unnecessary installations.

### 🔹 Create React App Steps

Let's walk through the steps!

1. Within your VSCode terminal, navigate to your project's main directory. If you are currently in the "backend" directory, navigate one directory higher by using the `cd ..` command. We need to do this because we're going to create a "frontend" directory that should be at the same level as our backend directory.

2. Run this command:
   ```bash
   npx create-react-app frontend
   ```
   
      - Once you permit it to start, it will begin installing the necessary packages for a React app.
   - You'll also see a message that "create-react-app" is deprecated, but we'll address that next term!
   - Once installation is complete, you'll see commonly used commands in the terminal, and in your file explorer you should see your new folder!

3. Before diving into all the files that were created in our frontend directory, let's start our development server to host our React app locally!

   - Navigate into your "frontend" directory and then run:
     ```bash
     npm start
     ```
     
     > 📝 **Note:** You may say, "WAIT!! That's the same command we used in the backend to start our server!" Yes, it is 😊
     >
     > Let's examine how that works. Remember that the package.json files manage the dependencies, metadata, and configuration of the directories they're within. If you look at "frontend/package.json," you'll see the "scripts" property. The value of that property is an object. Inside that object is the property "start" with the value "react-scripts start". This means when we run "npm start" in the frontend, we're actually running "react-scripts start," which starts our React app. 
     >
     > "npm start" is like a shortcut. In the backend's package.json, "npm start" causes "nodemon server.js" to execute. As developers, we can make "npm start" do whatever we need it to do.

### 🔹 Revealing the Magic as Science and Engineering

When started successfully, your terminal should inform you that your app "Compiled successfully!"

You'll then see the message "You can now view frontend in the browser."

This is followed by the URLs where you can view your app:

- Local: http://localhost:3000
- On Your Network: http://<your computer's IP address>:3000

> 💡 **Tip:** If your laptop and phone are on the same wi-fi and you type the "On Your Network" url into your phone's browser, you can see the react app on your phone!

This might feel like magic, but as technologists, we know it's science and engineering. Let's dive into a lower level of abstraction to understand how this works.

When you run `npm start` in a Create React App project, it uses the `react-scripts start` command, which is configured in the package.json file. This command starts a development server that serves your React application. Here's how it works:

#### 💻 Starting the Development Server

1. **Localhost:** When you run npm start, the development server starts on your local machine, typically on port 3000. This is why you can access your app at http://localhost:3000. The server is running on your machine, so you can view the app directly from there.

2. **On Your Network:** The message also shows a URL like http://10.0.0.152:3000, which is a computer's IP address on a local network. If this were your computers IP address, it would allow you to access the app from other devices connected to the same network.

#### 💻 How It Works

- **Localhost Access:** When you access http://localhost:3000, your browser sends a request to the development server running on your machine. The server responds by serving your React app, which is then rendered in the browser.

- **Network Access:** When you access the app from another device on the same network using the IP address (e.g., http://10.0.0.152:3000), the request is sent to your computer's IP address. Your development server receives this request and serves the app to the requesting device.

#### 💻 Technical Details

- Create React App automatically configures the development server to listen on both localhost and your machine's network IP address. This is why you see both URLs when you start the server.

- **Port Listening:** The server listens on port 3000, which is why you need to include the port number in the URL.

- **Firewall Settings:** Sometimes, accessing the app from another device might be blocked by your firewall settings. You may need to adjust these settings to allow incoming requests on port 3000.

#### 💻 Accessing from Mobile Devices

To access your app from a mobile device on the same network:

1. **Find Your Computer's IP Address:** Use commands like `ipconfig` (Windows) or `ifconfig` (Mac/Linux) to find your computer's IP address on the network.

2. **Open the App on Your Mobile Device:** Use the browser on your mobile device to navigate to the IP address and port number.

This setup allows you to test your React app on different devices without needing to deploy it to a production environment.

> 📝 **Note:** Revealing the inner workings of these tools, methods, and processes that form the infrastructure of our lives is just a google search or perplexity query away (don't use Chat GPT for research).

Alright 😊 Now is a perfect time to commit & push to our repo before we dive into all the directories and files that compose our frontend app.

## 📌 React App File Structure

Before moving forward read this overview of the react app default folder structure:
[https://github.com/CoachAsim/engineering-practicum-term-02-final-project-guide/blob/main/react-app-folder-structure-overview.md](https://github.com/CoachAsim/engineering-practicum-term-02-final-project-guide/blob/main/react-app-folder-structure-overview.md)

When we view our react app in the browser we see "Edit src/App.js and save to reload."

Let's follow those instructions.

1. Go back to VSCode and access the `src/App.js` file.

2. Change the body of the `<p>` element to be anything you want.

3. Save the changes and view your app in the browser. You should see that the text has changed!

We now know that **"App.js"** is our main application component:
- It's the "root" component that contains all other components
- It's where our app's main structure is defined

> 🎯 **Success!** You've now set up your React frontend! Before moving forward with development, let's commit our changes and then learn how to connect our frontend to our backend!
