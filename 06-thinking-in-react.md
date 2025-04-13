# 🧩 Thinking in React

## 🚀 Introduction

Ok! At this point you should understand the core concepts of React and be comfortable working with it!

Now we are going to learn how to "Think in React"! What this basically means is being able to see a mockup of a User Interface/User Experience and describe how that UI needs to be broken down into React components and how data should flow through them.

> 🚨 **IMPORTANT:** Before moving forward, you should have your user interface wireframes done in some software that you can edit. I preferred Canva for this project. Here is my example: [Air Quality Analyzer Wireframes](https://www.canva.com/design/DAGj4lEv0Ew/evindvdrenMViShzqZEEMQ/edit?utm_content=DAGj4lEv0Ew&utm_campaign=designshare&utm_medium=link2&utm_source=sharebutton)

> 📝 **Note:** Please do not get hung up on how your UI looks. That is not the focus of this project. If you find that designing UI/UXs is something that you are passionate about, then you can look more deeply into that field on your own time! It's an entire field of study on its own! If you're curious check out this video: [What is UI vs UX Design?](https://www.youtube.com/watch?v=TgqeRTwZvIo)

With those things checked off, we can get into it! 😊

To learn how to implement our UI we are going to use... you guessed it - documentation! The React docs have a page called "Thinking in React", so we are going to follow it.

## 📌 Our Approach

We are going to break our approach into 2 phases:

### 🔹 Static Phase:
1. Break the UI into a component hierarchy
2. Build a static version in React

### 🔹 State Phase:
3. Find the minimal but complete representation of UI state
4. Identify where your state should live
5. Add inverse data flow

As always, read through the documentation thoroughly, and apply the concepts in the guide to your own project step by step.

## 1️⃣ Static Phase
"It’s often easier to build the static version first and add interactivity later. Building a static version requires a lot of typing and no thinking, but adding interactivity requires a lot of thinking and not a lot of typing." - React Documentation

### 🔹 Step 1: Break the UI into a component hierarchy

Read: [React Documentation - Step 1: Break the UI into a component hierarchy](https://react.dev/learn/thinking-in-react#step1-break-the-ui-into-a-component-hierarchy)

> 💡 **Tip:** Before moving onto step 2 you should have your wireframes broken down into component hierarchies. See my example for reference: [Component Hierarchy Example](https://www.canva.com/design/DAGj5H-Nt5A/VXBsJowK42DTvZlhgC6tnA/edit?utm_content=DAGj5H-Nt5A&utm_campaign=designshare&utm_medium=link2&utm_source=sharebutton)

### 🔹 Step 2: Build a static version in React

Before you jump into step 2 let's talk about a couple libraries:

#### 🛠️ React Bootstrap

Making HTML look and feel good can be SUPER time consuming. So to save time and effort people create UI libraries. React Bootstrap is one of the most popular for React. I recommend using it to implement your components. 

> 🔗 Take a look at this video for an overview of how to do that: [React Bootstrap Tutorial](https://www.youtube.com/watch?v=8pKjULHzs0s)

### Going to different "pages"
You may be asking yourself, "wait how do I get my app to navigate to different pages when I click on links!!!???". Fear not, here is a guide that explains how to use React, React Router, and React Bootstrap to do that!
https://github.com/ahdeah/engineering-practicum-term-02-final-project-guide/blob/main/react-router-bootstrap-guide.md

You can also view my example project for reference:
https://github.com/ahdeah/detroit-msa-aq-analyzer-epfp2/blob/main/frontend/src/App.js

#### 📊 D3.js

Same concept applies to visualizing data. To code it from scratch would take forever so let's use a library. I recommend using D3.js. 

> 🔗 Check out this overview video: [D3.js in 100 Seconds](https://www.youtube.com/watch?v=bp2GF8XcJdY)

Equipped with those tools, go ahead and read step 2! 😊

Read: [React Documentation - Step 2: Build a static version in React](https://react.dev/learn/thinking-in-react#step-2-build-a-static-version-in-react)

## 2️⃣ State Phase

Alright from here you can go ahead and read then apply steps 3 through 5! Keep in mind, it's your responsibility to slow yourself down and take the time to understand concepts. If you have to go back and fill gaps in knowledge, that is 110% ok! It's the best thing to do! 😊

> 📝 **Note:** Right now we are not connecting our backend to our frontend. Here we are using placeholder/dummy data to define what our frontend should look like for the user. Then in future steps we will use our server to read our database, process our data, and then send that data to populate the frontend that we are creating now.

By the end of this section, you should basically have a working version of your UI that just needs to be populated with real data by making requests to your server, which I will guide you through later! 🚀

> 🎯 **Success!** When you have a working UI with placeholder data, you've successfully applied "thinking in React" to your project!
