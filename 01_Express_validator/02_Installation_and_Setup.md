 We are goning to use express validator to add some validations that prevent the user from sending unwanted data, like weak passwords, usernames with one letter, invalid emails, or leaving some of the fields empty.


## installation and setup

`npm install --save express-validator
`
- Inside ```api/users/route.js```:
  -  Import the body API from express-validator library to use its validation:
     ```js 
     const { body } = require('express-validator');
     ```


  - bewteen the “/signup” path and the singUp controller function we will add our validation, to make sure of the following:
     - The fields exist, they're not null or empty:
     ```js 
     exists({checkNull: true}).isNot().isEmpty()
     ```
      Here it’s gonna check if the field exists then it’s gonna check if it’s null if so it will throw an error      message, and will move on to the next               validation which is checking if the value is empty or not.
     - The email field is filled with a valid input  => isEmail()
     - The password is longer than 8 characters and is strong.
     - firsName and userName are not named with “name”.
    
   Wanna send a custom message?
    Just add a property 
    ```js
    .withMessage(“YOU_MESSAGE”).
    ```

    Now let’s apply the above validations 🥳
     
     ```js
     router.post("/signup",
     // we specify the field that we want to add the validation to in body arguments
     body("firstName")
        .exists({ checkNull: true })
        .not()
        .isEmpty()
        .custom((value, { req }) => {
       if (value?.toLowerCase() === "Name".toLowerCase())
         throw new Error(`You can't name it ${value}, ro7 wel`);

       return true;
     }),
    body(“lastName”)
        .exists({ checkNull: true })
        .not()
        .isEmpty(),
    body(“username”)
        .exists({ checkNull: true })
        .not()
        .isEmpty()
        .custom((value, { req }) => {
       if (value?.toLowerCase() === "Name".toLowerCase())
         throw new Error(`You can't name it ${value}, ro7 wel`);

       return true;
     }),
    body("email")
        .exists({ checkNull: true })
        .not()
        .isEmpty()
       .isEmail(),
    body("password")
        .exists({ checkNull: true })
        .not()
        .isEmpty()
       .isLength({ min: 8 })
     signUp);
    ```
    
    Now let’s get the validation results.
 - Inside `api/users/controller.js`  => signUp function, before creating the user we’re gonna get the validation result.
    

    Import the function `validationResult` from `express-validtor` library:
    ```js 
    const {validationResult} = require(“express-validator”)
    ```

    Store the result in a variable called validtionResults, and for now let’s console log it and see the result

   ```js 
   exports.signUp = async (req, res) => {
      try {
        const validtionResults = validationResult(req)
        console.log("🚀 ~ file: controllers.js ~ line 23 ~ exports.signUp= ~ errors", errors)
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

    Test it by breaking one of the rules (wrong password and firstName), what do you see?
    ```
    Result {
      errors: [
        {
          value: '',
          msg: '',
          param: 'password',
          location: 'body'
        },
        {
          value: '',
          msg: 'ERROR_MESSAGE',
          param: 'firstName',
          location: 'body'
        },
        
      ]
    }
    ```
 It’s an object of errors array that contains objects, and in each object we have a value of a specific field, error message, param which refers to the field name, as well as location.
So now let's send the errors messages as a response:

```js 
exports.signUp = async (req, res) => {
  try {
    const validationResults = validationResult(req)
     //we’re gonna check if the array of errors is empty or not
    if(!validationResults.isEmpty()){
    // if it’s not empty, we’re gonna send the array of errors with their messages as a response
    return res.status(400).json({
        errors: validation.errors.map((err) => ` ${err.param} : ${err.msg}`),
      });

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
Congradulations, it’s working.



