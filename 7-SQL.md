# SQL
- Là các table,  trong table có các fields được gọi là column (id, email, name, description).
- Data điền vào các field này được gọi là các records hay được xem là row
- SQL cho phép ta relate giữa các table. order có thẻ liên hệ với product và user.
- Tính chất của SQL :
  + Data Schema : data phải fix 1 schema.
  + Data Relation : One-To-One, One-To-Many, Many-To-Many.

# No SQL
- Không cần có schema.
- là các collections chứa các documents => collection User có document {name, title...};
- Không cần có relation.

# Horizontal and Vertical Scaling.
- Horizontal Scaling : Add more server. And merge data into 1 database.
- Vertical Scaling : improve Server Capacity / Hardware.

# Install SQL
- npm i mysql2
- mysql.createPool : quản lý nhiều connections.
-  set up mysql2 :

```
  // database.js
  const mysql = require('mysql2');

  const pool = mysql.createPool({
    host: 'localhost',
    use: 'root',
    database: 'node_basic',
    password: '123456'
  });
  
  module.exports = pool.promise();

  // app.js
  const db = require("./util/database");
  // Db
  db.execute('SELECT * FROM products')
    .then((result) => {
      console.log(result);
    })
    .catch((err) => {
      console.log(err);
    });

```

# Sequelize
- Là 1 thư viện Object-Relational Mapping Library.
- Thay vì viết các câu lệnh queries ta sẽ dùng js object.
- Các định nghĩa :
  + Model : class Product, User
  + Instance : new Product()
  + Queries : User.findAll()
  + Associations : User.hasMany(Product)

 - Install : npm i sequelize

```
const Sequelize = require('sequelize');


const sequelize = new Sequelize('node_basic', 'root', '123456', {
  dialect: 'mysql',
  host: 'localhost',
});

module.exports = sequelize;

```

- Tạo model :

```
const Sequelize = require("sequelize");

// được khởi tạo như ở trên.
const sequelize = require("../util/database");

const Product = sequelize.define("product", {
  id: {
    type: Sequelize.INTEGER,
    autoIncrement: true,
    allowNull: false,
    primaryKey: true,
  },
  title: Sequelize.STRING,
  price: {
    type: Sequelize.DOUBLE,
    allowNull: false,
  },
  imageUrl: {
    type: Sequelize.STRING,
    allowNull: false,
  },
  description: {
    type: Sequelize.STRING,
    allowNull: false,
  },
});

module.exports = Product;

```

- ở app.js
```
const sequelize = require("./util/database");

sequelize
  .sync()
  .then((result) => {
    app.listen(3000);
  })
  .catch((err) => {
    console.log(err);
  });

```

# Associations
- Mối liên hệ giữa các model với nhau.
- Product có thể thuộc nhiều Cart hay User có nhiều product.
- Để tạo associations : fiel app.js
```
  const User = require("./models/user");
  const Product = require("./models/product");

  Product.belongsTo(User, {constraints: true, onDelete: 'CASCADE'});
  User.hasMany(Product);

  sequelize
  .sync({force: true})
  .then((result) => {
    app.listen(3000);
  })
  .catch((err) => {
    console.log(err);
  });
```

- force sẽ khi đề cái hiện tại , bỏ đi khi đã ghi đè.
- Lưu ý : sequelize sẽ chỉ chạy khi khởi tạo, chứ ko chạy mỗi khi req đến.
- 








