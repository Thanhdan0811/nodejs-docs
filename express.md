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

