# Install Express
- npm i express --save

```
  
  const http = require('http');
  const express = require('express');

  const app = express();
  /app.listen(3000);

  /// const server = http.createServer(app);
  //  server.listen(3000);

```

# Middleware
- Express chủ yếu là middleware.
- Express có thể hiểu là 1 request đến và được truyền đi qua các function (middleware) được cấp vởi express, cho tới khi gửi response.
- app.use : cho phép ta add 1 middleware funciton.


```
 app.use((req, res, next) => {
  console.log("In the middleware");
  next();
});

app.use((req, res, next) => {
  console.log("In Another the middleware");
});

```

- Cần gọi next() để có thể đi đến middleware tiếp theo.

# handling different routes
```

app.use("/product", (req, res, next) => {
  console.log("In Another the middleware");
  res.send(`
    <h1>Hello from Express Product</h1>
  `);
});

app.use("/", (req, res, next) => {
  console.log("In Another the middleware");
  res.send(`
    <h1>Hello from Express</h1>
  `);
});

```

- Việc set route sẽ chạy từ đầu fille đến cuối file.
- Vì ko gọi next() , nên khi vào /product nó sẽ ko vào / ở dưới.


# Parsing Incoming request
- để xử lý body được gửi đến, ta dùng thư viện body-parser
- npm i -save body-parser
- để lấy dữ liệu được gửi đến server.

```
const http = require("http");
const express = require("express");
const bodyParser = require("body-parser");
const app = express();

// ====================
app.use(bodyParser.urlencoded({ extended: false }));

app.use("/add-product", (req, res, next) => {
  res.send(`
    <form action="/product" method="POST">
      <input type="text" name="title" />
      <button type="submit">Add product</button>
    </form>
  `);
});

app.use("/product", (req, res, next) => {
  console.log(req.body);
  res.redirect("/");
});

app.use("/", (req, res, next) => {
  res.send(`
    <h1>Hello from Express</h1>
  `);
});

```

- console.log(res.body) => { title: '14213213' 
# Middleware for POST request.
- Ta dùng app.post để chỉ chạy route cho post

```
app.post("/product", (req, res, next) => {
  console.log(req.body);
  res.redirect("/");
});

```

# Express Router

- admin.js
```
  const express = require("express");

const Router = express.Router();

router.get("/add-product", (req, res, next) => {
  res.send(`
    <form action="/product" method="POST">
      <input type="text" name="title" />
      <button type="submit">Add product</button>
    </form>
  `);
});

router.post("/product", (req, res, next) => {
  console.log(req.body);
  res.redirect("/");
});

module.exports = router;  
  
```

- app.js
```
  const app = express();

  const adminRoutes = require("./routes/admin");
  const shopRoutes = require("./routes/shop");

  app.use(bodyParser.urlencoded({ extended: false }));

  app.use(adminRoutes);
  app.use(shopRoutes);

app.listen(3000);
```

- NOTE : ở dây ta dùng router.get, router.post sẽ check exact match cho route, 

# Filtering Path 

```
app.use(shopRoutes);
app.use("/admin", adminRoutes);

```

- các path trong admin sẽ bắt đầu với /admin.

# HTMLL file , sendFFile
- path tron nodejs sẽ chỉ đến os , "/views" từ os tìm views.
- Ta sẽ dùng path và __dirname trong node.

```

const path = require('path');
const express = require("express");
const router = express.Router();

router.get("/", (req, res, next) => {
  // res.send(`
  //   <h1>Hello from Express</h1>
  // `);
  res.sendFile(path.join(__dirname, ',,/' ,'views', 'shop.html'));
});

module.exports = router;

```
- Dùng path.join để xử lý case là \ hoặc / trong path đối với máy window.
- __dirname trả về path của file đagn dùng nó.


# Helper function path

- path.js
```
  const path = require("path");
  // module.exports = path.dirname(process.mainModule.filename);
  module.exports = path.dirname(require.main.filename);

```

- admin.js
```
const rootDir = require("../utils/path");

router.get("/add-product", (req, res, next) => {
  // res.sendFile(path.join(__dirname, "../", "views", "add-product.html"));
  res.sendFile(path.join(rootDir, "views", "add-product.html"));
});

```

# Serving files Statically
- staticlly có nghĩa là sẽ không bị express xử lý như là routes.
- Sẽ được xem là file system.

```
  app.use(express.static(path.join(__dirname, 'public')));
  
  => <link rel="stylesheet" href="/css/main.css">
```

- 
