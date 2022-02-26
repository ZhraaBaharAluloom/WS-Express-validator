Don’t you think the project became a bit messy? What if we keep adding validations? 
The code will get bigger and bigger, so I’m gonna clean it up a bit

- In `api/users`, create a new file named `validation.js`

And inside that file we’re gonna add everything related to users validation 

```js
const { body, validationResult } = require('express-validator')



const userValidationRules = () => {
  return [
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

     ]
}

const validate = (req, res, next) => {
  const validationResults = validationResult(req);
  if (validationResults.isEmpty()) {
    return next();
  } else {
    return res.status(400).json({
      errors: validationResults.errors.map(
        (err) => ` ${err.param} : ${err.msg}`
      ),
    });
  }
};

module.exports = {
  userValidationRules,
  validate,
}
```

- In `api/users/routes.js` import those functions:

```js
const { userValidationRules, validate } = require('./validation.js')
```
and call them bewteen the path and the controller function
```js
router.post("/signup",
userValidationRules(), validate, signUp)
```
That not it! There’s more. Check the [express-validator](https://express-validator.github.io/docs/index.html) docs if you're interested, and I’m sure you are 😉.




