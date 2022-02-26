Don’t you think the project became a bit messy? What if we keep adding validation? 
The code will get bigger and bigger and ewww, so I’m gonna clean a bit

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
  const validationResults = validationResult(req)
    // we’re gonna check if the array of errors is empty or not
    if(!validationResults.isEmpty()){
    // if it’s not empty, we’re gonna send the array of errors with their messages as a response
    return res.status(400).json({
        errors: validation.errors.map((err) => ` ${err.param} : ${err.msg}`),
      });
    }else {
return next()
}

}

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
ANNNDDD THHHAT’S NOT IT, there’s more and more, check the [express-validator](https://express-validator.github.io/docs/index.html) docs if you're interested and I’m sure you are 😉.




