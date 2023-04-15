---
title: "Setting up the Express Server"
seoTitle: "setting up express server"
seoDescription: "How to set up server in Express js?"
datePublished: Thu Apr 13 2023 20:07:43 GMT+0000 (Coordinated Universal Time)
cuid: clgfjy6ws000109l96xvqckq8
slug: setting-up-express-server
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1681416287325/57be0757-6f1c-4b3d-8192-6f520ebb8d0e.png
tags: express, server, nodejs, backend

---

This Blog is going to be all about setting up a basic backend server. This blog consists of steps required to set up a basic backend server using Express js. After reading this blog creating an express server is going to be a piece of cake for you.

So let's explore all the steps one by one...

---

# Making the project ready

### 1- Create a folder named ExpressServer

("You can give any name to your folder ")

```plaintext
mkdir ExpressServer
```

### 2- Initialize the project and create a package.json file-

<details data-node-type="hn-details-summary"><summary>What is a package.json file ?</summary><div data-type="detailsContent">"The package.json file is <strong>the heart of any Node project</strong>. It records important metadata about a project which is required before publishing to NPM, and also defines functional attributes of a project that npm uses to install dependencies, run scripts, and identify the entry point to our package." <a target="_blank" rel="noopener noreferrer nofollow" href="https://heynode.com/tutorial/what-packagejson/#:~:text=Recap-,The%20package.,entry%20point%20to%20our%20package." style="pointer-events: none">Read more here</a></div></details>

```plaintext
npm init -y
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1681412344854/a66f600b-a321-427d-a065-18e275c57bdb.png align="center")

### 3- Installing Express js in our project

```plaintext
npm install express
```

This command installs Express in your project.

[What is Express js?](https://expressjs.com/)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1681412538292/9eca4f95-c125-4935-8c1c-ce93e417642c.png align="center")

### 4- Setting up express

Now it's time to hop into our code editor and add a few lines of code to set up our server.

* **Create a file named index.js in our root directory**
    
    *index.js* file is the entry point to our project where we will write code to run our server
    

**content of index.js file goes like this**

```javascript
const express=require('express');
const app=express();
const PORT=5000;
app.listen(PORT, function(err){
   if (err) console.log(err);
   console.log("Server listening on PORT", PORT);
});
```

**Now Let's break it line by line what is happening here**

```javascript
var express = require('express'); // Requires the Express module just as you require other modules and and puts it in a variable.
```

.

```javascript
var app = express(); // with the help of app variable we will be able to access the functionalities of express module
```

```javascript
const PORT=5000;
app.listen(PORT, function(err){
   if (err) console.log(err);
   console.log("Server listening on PORT", PORT);
}); 
```

The app.listen() method takes a port and a callback function and binds itself with a specified port and listens for any connections. In simple words it tells the server to listen to port 5000.

In case of any error we print the error else we print a confirmation message saying "Server listening on PORT 5000"

If the port is not defined or 0, an arbitrary unused port will be assigned by the operating system that is mainly used for automated tasks like testing, etc.

Open terminal and run command ***"node index.js"*** to start our server.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1681414543966/adb23570-8ae6-485c-afeb-4370afb17ed6.png align="center")

Hurray 🥳 our server is running at port 5000 without any error.

### 5- Accessing our server from browser

Finally, It's time to check whether server is running or not.

Make a GET request to URL [http://localhost:5000](http://localhost:5000/) from browser.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1681414891305/dac1aa22-d87b-4e40-a86e-599250e6b927.png align="center")

OOPS! we are getting an error saying " ***Cannot GET / "***

This is because we do not have any endpoint for "/" route. Lets add few more lines of code to set up a "/" route.

```javascript
app.get("/",(req,res) =>
{
    return res.status(200).json("Server Running Successfully");
})
```

The **app.get()** function routes the HTTP GET Requests to the path which is being specified with the specified callback functions.

Save the code and run server again using ***" node index.js "***

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1681415342420/ba302844-368f-45a3-87e0-728666694ee7.png align="center")

Hurray!🎇 Our express server is set up and we are able to make request to it from our browser...

### RECAP

1. Create a folder and initialise project in it.
    
2. install express library and add below code in index.js file
    
    ```javascript
    const express=require('express');
    const app=express();
    
    app.get("/",(req,res) =>
    {
        return res.status(200).json("Server Running Successfully");
    })
    
    app.listen(5000, function(err){
        if (err) console.log(err);
        console.log("Server listening on PORT", 5000);
     });
    ```
    
3. run command "node index.js"
    
4. Console Output :
    
    **Server listening on PORT 5000**
    

In case of any error or any mistake please report it to me.

Follow for more interesting blogs till then Good Bye...