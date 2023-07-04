- tạo middleware để check validation
- validate ở client side (optional).
- validate ở server side.
- Mongodb cũng có built-in validation.

# Set up.
- npm i express-validator


```
/// auth.js routes
const { check, body } = require("express-validator/check");

router.post('signup', check('email')
                            .isEmail()
                            .withMessage('Please enter a valias email')
                            .custom((value, {req}) => {
                                if(value === "test@test.com") {
                                    throw new Error('This email address is forbidden');
                                }
                                return true; /// NOTE this.
                            }),
                            body('password', 'Please enter a password with only number. // this errorr is defaut').isLength({min: 5}).isAlphanumeric()
            , authController.postSignup);

// auth.js controllers
const { validationResult } = require('express-validator/check');

exports.postSignup = (req, res, next) => {
    const errors = validationResult(req);

    if(!errors.isEmpty()) {
      return res.status(422).render('auth/signup', {
        path: '/signup',
        errorMes: errors.array()[0].msg,
      })
    }

}

/// errors.array()= > [ { location: 'body', param: 'email', value: 'test', msg: 'Invalid value' } ]
/// < form nonvalidate></form> => chặn validate mặc định của form.

```

# Check for fields Equality.

```
/// auth.js routes
const { check, body } = require("express-validator/check");

router.post('signup', check('email')
                            .isEmail()
                            .withMessage('Please enter a valias email')
                            .custom((value, {req}) => {
                                if(value === "test@test.com") {
                                    throw new Error('This email address is forbidden');
                                }
                                return true; /// NOTE this.
                            }),
                            body('password')
                            .isLength({min: 5})
                            .isAlphanumeric(),
                            body('comfirmPassword')
                            .custom((value, {req}) => {
                                if(value !== req.body.password) {
                                    throw new Error('Password have to match');
                                }
                                return true;
                            })
            , authController.postSignup);

// auth.js controllers
const { validationResult } = require('express-validator/check');

exports.postSignup = (req, res, next) => {
    const errors = validationResult(req);

    if(!errors.isEmpty()) {
      return res.status(422).render('auth/signup', {
        path: '/signup',
        errorMes: errors.array()[0].msg,
      })
    }

}

/// errors.array()= > [ { location: 'body', param: 'email', value: 'test', msg: 'Invalid value' } ]
/// < form nonvalidate></form> => chặn validate mặc định của form.

```

# Adding Async Validation


```
/// auth.js routes
const { check, body } = require("express-validator/check");

router.post('signup', check('email')
                            .isEmail()
                            .withMessage('Please enter a valias email')
                            .custom((value, {req}) => {
                                /// async
                                return User.findOne({email: value})
                                    .then(userDoc => {
                                        if(userDoc) {
                                            return Promise.reject("Email existed")
                                        }
                                    })
                            }),
                            body('password')
                            .isLength({min: 5})
                            .isAlphanumeric(),
                            body('comfirmPassword')
            , authController.postSignup);


```

# Sanitizing

```
/// auth.js routes
const { check, body } = require("express-validator/check");

router.post('signup', check('email')
                            .isEmail()
                            .withMessage('Please enter a valias email')
                            .custom((value, {req}) => {
                                /// async
                                return User.findOne({email: value})
                                    .then(userDoc => {
                                        if(userDoc) {
                                            return Promise.reject("Email existed")
                                        }
                                    })
                            }),
                            body('password')
                            .isLength({min: 5})
                            .isAlphanumeric(),
                            body('comfirmPassword').trim().normalizeEmail(),
            , authController.postSignup);


```
