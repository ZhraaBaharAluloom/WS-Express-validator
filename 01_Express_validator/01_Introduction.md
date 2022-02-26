express-validator is a set of express.js middlewares that wraps validator and sanitizers functions.
In other words it’s a library that enables you to validate the requests. It allows you to use built-in validations and customize your validations! 
Isn’t it amazing? 


## How it works

Once the user sends a request, the express-validator will be triggered and will validate the request and report any errors before it reaches the DB, (all validations will happen between the request and the response). The request will never get to the DB unless it passes all validations successfully.

## Create user schema with its controller and routes
 
 1. Create an express project, connect it to you mongoDB, and install all required stuff.
 2. Make sure that everything is working without any errors, and the project is connected to the db.
 
 - create a user schema that has the following fields:


    ```js
    const mongoose = require("mongoose");

    const UserSchema = new mongoose.Schema(
      {
        username:  String,
        password: String,
        email: String,
        firstName: String,
        lastName: String,
      },
      { timestamps: true }
    );

    module.exports = mongoose.model("User", UserSchema);
    ```
  - Create a controller and route files for signing up.

Create a folder named api. Then inside it, create another folder called users (which contains the apis related to users). Inside the users folder, create two files:
      1. controllers.js.
      2. routes.js.
 
In the controllers.js file, create a function for sign up and don’t forget to install `bcrypt` to hash the password and `jsonwebtoken` to generate a token.

```js
const User = require("../../models/User");
const bcrypt = require("bcrypt");
const jwt = require("jsonwebtoken");

const generateToken = (user) => {
  const payload = {
    username: user.username,
    email: user.email,
    firstName: user.firstName,
    lastName: user.lastName,
    username: user.username,
    id: user.id,
    exp: Date.now() + 90000,
  };
  return jwt.sign(payload, "asupersecretkey");
};

exports.signUp = async (req, res) => {
  try {
    const hashedPassword = await bcrypt.hash(req.body.password, 10);
    req.body.password = hashedPassword;
    const newUser = await User.create(req.body);
    const token = generateToken(newUser);
    return res.status(201).json({ token: token });
  } catch (error) {
    return res.status(500).json({ message: error.message });
  }
};
```


In the `routes.js` file, create a route for sign up, and import the `signUp` function from controllers.

```js
const express = require("express");
const router = express.Router();
const { signUp } = require("./controllers");

router.post("/signup", signUp);


module.exports = router;
```

Don’t forget to import and run your userRoutes in app.js to activate it.
Now let’s sign up in postman to make sure that everything is working correctly.

If you got the token, you did it correctly and you can move to the next step which will be using validations.


