- tạo middleware để check validation
- validate ở client side (optional).
- validate ở server side.
- Mongodb cũng có built-in validation.

# Set up.
- npm i express-validator


```
/// auth.js routes
const { check } = require("express-validator/check");

router.post('signup', check('email').isEmail().withMessage('Please enter a valias email'), authController.postSignup);

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
