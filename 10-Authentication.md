# signup 

```
  User.findOne({email: email})
    .then(userDoc => {
      if(userDoc) {
          return res.redirect('/signup');
      }
      const user = new User({...data});
      return user.save();
    })
    .catch()

```

# Encrypting Passwords
- npm i bcryptjs


```
  const bcrypt = require('bcryptjs');

  User.findOne({email: email})
    .then(userDoc => {
      if(userDoc) {
          return res.redirect('/signup');
      }
      return bcrypt.hash(password, 12)
              .then(hashedPass => {
                const user = new User({...data, password: hashedPass});
                return user.save();
              })
    })
    .catch()

```

- Đảo ngược :

```
  bcrypt.compare(password, user.password)
    .then(doMatch => {
      if(doMatch) {
        req.session.isLoggedIn = true;
        req.sesssion.user = user;
        return req.session.save(err => {
          consoe.log(err);
          return res.redirect('/');
        })
      }
       res.redirect('/login');
    })
    .catch(err => {
      console.log(err);
      res.redirect('/login');
    })
```

- doMatch có thể là false hoặc true;

# Protect Routes.


```
  /// Tạo folder middleware tạo file is-auth.js
  exports.module = (req, res, next) => {
      if(!req.session.isLoggedIn) {
        return res.redirect('/login');
      }
      next();
  }

  /// ở app.js
  const isAuth = require(../middleware/is-auth);
  router.get('/add-product', isAuth, adminController.getAddProduct);  

```

# CSRF Attack
- Cross-Site Request Forgery : 1 trang web giả có thể dùng session của user, bằng cách user truy cập vào trang giả mạo.
- Trang giả mạo sẽ là cầu nối giữa user và Server.
- Để khắc phực thì ta sẽ chỉ cho phép khi mà user dùng view của ta.
- Ta sẽ dùng CSRF token.
- npm i csurf  => hiện đã ko còn dùng package này, https://www.npmjs.com/package/csrf-csrf có thể dùng này.
- ta có thể embedded token string vào form và server sẽ check token này.
- trang fake sẽ khó mà lấy được, do token sẽ được làm mới mỗi khi page được render.

```
  const csrf = require('csurf');

  /// middleware
  const csrfProtection = csrf();

  app.use(session(...));
  app.use(csrfProtection)

  //// shop.js
  res.render('shop/index', {
    ...,
    csrfToken: req.csrfToken()
  });

  //// in view index.ejs : name phải là _csrf
  <input type="hidden" name="_csrf" value="<%= csrfToken %>" />
  

```

-  Set cho tất cả res tới views
- res.locals set biến gửi đến view.
```
  //// app.js
  app.use((req, res, next) => {
    res.locals.isAuthenticated = req.session.isLoggedIn;
    res.locas.csrfToken = req.csrfToken();
    next();
  })

```


# flash messages
- Ta mún gắn error cho /login khi login không đúng.
- Gắn error message vào session 1 cách tạm thời và remove đi khi đã hiện error.
- npm i connect-flash

```
  /// app.js
  const flash = require('connect-flash');

  app.use(session(...));
  app.use(csrfProtection);
  app.use(flash());

  /// auth.js
  exports.postLogin = (req, res, next) => {
    User.findOne({email: email})
      .then(user => {
        if(!user) {
          req.flash('error', 'Invalid email.')
          return res.redirect('/login');
        }
      })
  }

  exports.getLogin = (req, res, next) => {
    res.render('auth/login', {
      path: '/login',
      errorMessage: req.flash('error')
    });
  }

  /// login.ejs
  

```

# Reset password.
- cần gửi mail và token để reset lại mk.
- token nên được lưu trong database user.
```
  const crypto = require("crypto");

  exports.postReset = (req, res, next) => {
    crypto.randomBytes(32, (err, buffer) => {
        if(err) return res.redirect('/reset');
        const token = buffer.toString(hex);
        User.findOne({email: req.body.email})
          .then(user => {
              if(!user) return res.redirect('/reset');
              user.resetToken = token;
              user.resetTokenExpiration = Date.now() + 3600000;
              return user.save();
          })
          .then(result => {
              transporter.sendMail({
                to: req.body.email,
                from: "ccc",
                html: `
                  <p> Click <a href="http://localhost:3000/reset/${token}">Link</a> <p>
                `
              })
          })
    });
  }

  /// check
  exports.getNewPassword = (req,res, next) => {
    const token = req.params.token;
    User.findOne({resetToken: token, resetTokenExpiration: { $qt: Date.now() }})
      .then((user) => {
        
      })
  }

  // new pass
  exports.postNewPassword = (req, res, next) => {
    let resetUser;
    User.findOne({
      resetToken: req.body.passwordToken,
      resetTokenExpiration: {$qt: Date.now()},
      id: userId
    })
      .then(user => {
        resetUser = user;
        return bycrypt.hash(newPassword, 12);
      })
      .then(hashedPassword => {
        resetUser.password = hashedPassword;
        resetUser.resetToken = undefined;
        return resetUser.save();
      })
  }

```

# Authorization

















