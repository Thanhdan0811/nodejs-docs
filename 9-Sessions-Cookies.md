# What is Cookies ?
- Khi người dùng đăng nhập có thể lưu ở đâu đó thể lưu là ng dùng đang đăng nhập.
- Ta có thể gửi lại cookí thong qua res header, để user có thể gửi kèm request.
- request sẽ bị reset sau khi gửi response. Mỗi request sẽ là riêng biệt.
- Cookie sẽ giúp ta lưu data ở browser,

```
  res.setHeader('Set-Cookie', 'loggedIn=true'; Max-Age=10; Secure; httpOnly);
  res.redirect('/');

  //
  exports.getLogin = (req, res, next) => {
    const isLoggedIn = req.get('Cookie').split(';')[1].trim().split('=')[1];
  }
```
- Secure sẽ chỉ được set thông qua https.
- httpOnly : ta sẽ không thể access cookie thông qua Javascript ở client.

# What is Sessions 
- Lưu thông tin ở session ở server.
- Client cần cho server biết session mà nó thuộc về.
- Ta có thể dùng cookie để lưu id của session.
- install sesssion.

- npm install express-session

```
  const session = require('express-session');
  app.use(session({
    secret: 'my secret',
    resave: false,
    saveUninitailized: false
  }));

```

- resave : session sẽ ko save mỗi lần req hay res.
- saveUninitailized:


```
    exports.postLogin = (req,res,next) => {
      req.session.isLoggedIn = true;
    }

    console.log(req.session.isLoggedIn);
  

```

# Save session in mongoDb
- npm i connect-mongodb-session

```
  const session = require('express-session');
  const MongoDbStore = require('connect-mongodb-session')(session);
  const store = new MongoDbStore({
    uri: 'mongodb+srv....',
    collection: 'sessions'
  });


  app.use(session({
    secret: 'my secret',
    resave: false,
    saveUninitailized: false,
    store: store,
  }));

```

- xóa session :
- req.session.destroy(() => {  req.redirect('/'); })

## store là mongoDb nên sẽ ko lưu các methods từ mongoose.

```
  //// login controller
    exports.postLogin = (req, res, next) => {
      User.findById('123123h12k3h')
        .then(user => {
          req.session.isLoggedIn = true;
          req.session.user = user;
          req.session.save((err) => {
            console.log(err);
            res.redirect('/');
          });
        })
    }
  ////

  app.use((req, res, next) => {
    if(!req.session.user._id) return next();
    User.findById(req.session.user._id)
      .then(user => {
        req.user = user;
        next();
      })
  })

```










