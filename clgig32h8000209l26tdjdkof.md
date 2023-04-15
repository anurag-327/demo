---
title: "Authentication with Express, Mongodb and jsonwebtoken"
datePublished: Sat Apr 15 2023 20:42:51 GMT+0000 (Coordinated Universal Time)
cuid: clgig32h8000209l26tdjdkof
slug: authentication-with-express-mongodb-and-jsonwebtoken
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1681591361765/5b4638ba-91b0-4ef1-b692-78529cec46ee.jpeg
tags: mongodb, nodejs, jwt, expressjs-cilb5apda0066e053g7td7q24, crypto-js

---

Hello Everyone and welcome back to the new Blog. Today we will learn how to implement authentication in Node js using express js and MongoDB Database.

This blog is going to be fully technical but you don't need to worry just stay with me for the next few minutes and i will be making setting up authentication super easy for you.

---

# Packages Used:

1. **express**  
    Express is a minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications
    
2. **express-validator**  
    To Validate the body data on the server in the express framework, we will be using this library. It's a server-side data validation library. So, even if a malicious user bypasses the client-side verification, the server-side data validation will catch it and throw an error.
    
3. **crypto-js**  
    This library will be used to hash the password and then store it to database.This way even app administrators can't access the account of a user.
    
4. **jsonwebtoken**  
    **jsonwebtoken** will be used to encrypt our data payload on registration and return a token. We can use that **token** to authenticate ourselves to secured pages like the dashboard. There would also an option to set the validity of those token, so you can specify how much time that token will last.
    
5. **mongoose**  
    Mongoose is a MongoDB object modeling tool designed to work in an asynchronous environment.
    

# Setting Up the Project

### 1- Setting up express js server

If you are new to setting up the express server, you don't need to worry I had already written a blog for you.

This is the folder structure that we are going to use in our Project.

* **config**\- for all configuration files
    
    ***mongoose.js***\- code to connect to MongoDB
    
* **controller** - for all the route controller
    
    ***verifytoken.js***\- to create a controller to verify tokens that we generated
    
* **model** - for the schema for our database
    
    ***user.js*** - to set user schema
    
* **routes**
    
    ***auth.js*** - this contains all the routes required for authentication
    
* **index.js** - which is the entry point of our project
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1681585095623/35ce19e5-06c0-48a0-a817-4991be94365c.png align="center")

%[https://knightblogs.hashnode.dev/setting-up-express-server] 

The code for setting up the express server goes like this.

```javascript
const dotenv=require("dotenv").config();
const express=require("express");
const app=express();
app.use(express.json)

app.listen(process.env.PORT||5000,(err) =>
{
    if(err)
    console.log("Error in runing server")
    console.log("Server running at PORT",process.env.PORT)
})
```

### 2- connecting to the MongoDB database

The following code is to be written to connect your project to the local MongoDB database.

```javascript
const mongoose=require("mongoose");
mongoose.set('strictQuery', false);
mongoose.connect(process.env.MONGO_URL)
.then(()=>
{
    console.log("Database connected successfully")
})
.catch(err => console.log("Error connecting Database"));
```

so you might be thinking what is this "**process.env.MONGO\_URL".** MONGO\_URL is the URL to which our database is connected, here it is the URL of the local MongoDB database.

In our Scenario,

```plaintext
MONGO_URL=mongodb://0.0.0.0:27017/AuthenticationDb
```

which means we are trying to connect to the local MongoDB database named AuthenticationDb.

### 3- Deciding the user schema for our database

After connection to our database one of the most important things is deciding the user schema of data of the user.

so our user details consist of 3 fields {username, email and password}.

so we write the below code to declare schema for our users in "***model/user.js"*** which has 3 major fields username, email and password.

```javascript
const mongoose=require("mongoose")
const userSchema=new mongoose.Schema({
    username:{
        type:String,
        required:true
    },
    email:{
        type:String,
        required:true,
        unique:true
    },
    password:
    {
        type:String,
        required:true,
    },
},{
    timestamps:true
})

module.exports=mongoose.model("user",userSchema);
```

### 4- Setting routes and logic for SignUp

So we have declared our schema and now it's time to implement our logic for the signup function.

But before implementing the logic we need to set up routes for our login and signup functions.

**NOTE:** We have used the **"routes/auth.js"** file to write our logic for login and signup functionalities.

The basic flow chart for signup functionality goes like this.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1681586360023/7fa8f3ac-7d0d-455f-9b55-967aab3f945c.png align="center")

```javascript
router.post("/signup",[
    check('email', 'Email cannot be empty').isEmail(),
    check('username', 'Name cannot be empty'),
    check('password', 'Password length should be 5 to 10 characters').isLength({ min: 5, max: 10 })
],
async(req,res) =>
{ 
    try
    {  
        const errors = validationResult(req);
        if (!errors.isEmpty()) 
        {
            res.json(errors)
        }
        const {username,email,password}=req.body;
        const userDetail=await User.findOne({email})
        if(userDetail)
        {
            return res.status(401).json({status:409,message:"User Already Exists"})
        }
        else
        {
            const newUser=new User({
               username,email,                   password:CryptoJS.AES.encrypt(password,process.env.CRYPTOJS_SEC_KEY).toString()
            })
            const result=await newUser.save();
            if(result)
            return res.status(201).json({status:201,token:tokengenerator(result._id)});
            else
            return res.status(500).json({status:500,message:"error registering user"});
        }
        
    }catch(err){
        console.log(err.message)
        return res.status(500).json({status:500,message:"Server Error while processing request"})
    }
})
```

### 5- Setting routes and logic for login

So the basic flowchart for login goes like this.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1681587082523/c2b20586-4c0e-432e-9e10-53c47dfcb9b6.png align="center")

The code for the above logic goes like this.

```javascript
router.post("/login",async(req,res) =>
{
    try{
        const {email,username,password}=req.body;
        const userDetail= await User.findOne({
            $or: [{
                email:email
            }, {
                username:username
            }]
        })
        if(userDetail)
        {
            const decryptedpassword=CryptoJS.AES.decrypt(userDetail.password,process.env.CRYPTOJS_SEC_KEY).toString(CryptoJS.enc.Utf8)
            if(password===decryptedpassword)
            {
                return res.status(200).json({status:200,token:tokengenerator(userDetail._id)});
            }
            else
            {
                return res.status(403).json({status:403,message:"Wrong password"});
            }
        }
        else
        {
            return res.status(404).json({status:404,message:"User doesnot exist"});
        }
    }catch(err){
        console.log(err.message)
        return res.status(500).json("Internal Error")
    }
})
```

**NOTE: we have created a token generator function to generate jwt token corresponding to given user id.**

```javascript
function tokengenerator(_id)
{
     return jwt.sign({_id:_id},process.env.JWT_SEC_KEY,{expiresIn:"3d"});
}
```

**6- Wrapping up all the functionalities**

Till now we have created the logic for authentication and now it's time we will connect the routes to our server.

```javascript
const dotenv=require("dotenv").config();
const express=require("express");
const app=express();
app.use(express.json)
const ConnectMongoDb=require("./config/mongoose")
app.use("/auth",require("./routes/auth"));
app.listen(process.env.PORT||5000,(err) =>
{
    if(err)
    console.log("Error in runing server")
    console.log("Server running at PORT",process.env.PORT)
})
```

**app.use("/auth",require("./routes/auth"))-** It is a middleware that states that all the requests coming to route /auth should be directed to the auth.js file in the routes folder.

### 7- Bonus Functionality

We have generated a jwt token corresponding to user id and to verify that token we need to create a middleware that checks whether the given token is valid or not.

This is just the opposite of signing a jwt token. here we extract details from the generated token and save the user ID into req.user.

**NOTE:** Incoming token is assumed to be of the following format **"Bearer XXXXXXXXXXXXXXX"**

```javascript
module.exports.verifyToken=async(req,res,next) =>
{
    try{
        const authHeader=req.headers.authorization
        if(authHeader && authHeader.startsWith("Bearer"))
        {
            try{
                const token=authHeader.split(' ')[1];
                jwt.verify(token,process.env.JWT_SEC_KEY,async (err,user) =>
                {
                    if(err) return res.status(403).json({status:403,mesaage:"Invalid token"});
                    req.user={_id:user._id};
                    next();
                })
            }catch(err)
            {
                return res.status(401).json({status:401,mesaage:"authorisation failed"})
            }
        }
        else
        {
            return res.status(401).json({status:401,mesaage:"No token provided/Invalid token"})
        }
    }catch (err) 
    {
        return res.status(500).json({status:500,mesaage:err.message})
    }
}
```

### 8- Testing Time

The project has been tested on POSTMAN so you need to have postman installed to test the api's.

Sign Up functionality

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1681589901591/3b4794a3-3507-4073-8d50-3433f6be85dd.png align="center")

Login Functionality

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1681589961556/324ee60f-cbe5-4ab9-b73d-3f3b5134ea60.png align="center")

Getting Details of the user after verifying the token

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1681590876968/356f8b5b-c235-4fb9-9971-457c5f1d192d.png align="center")

In case of any issues feel free to contact me.

Here is the link to the full code of this blog.

%[https://github.com/anurag-327/hashnode/tree/main/Authentication]