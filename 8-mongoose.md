# install 
- npm i mongoose

```
  mongoose.connect("url-database").then(result => app.listen(3000))

```

- Tạo model :

```
  const mongoose = require("mongoose");
  consst Schema = mongoose.schema;
  const productSchema = new Schema({
    title: {
      type: String,
      require: true
    },
    price: {
      type: Number,
      requrie: true
    },
    cart: {
      items : [{productId : {type: Schema.Types.ObjectId, required: true }, }]
    }
  });

  module.exports = mongoose.model('Product', productSchema);

////

  const Product = require("../models/product");
  const product = new Product({...data});

  prouduct.save().then()

///

  Product.findById(prodId).then(product => { product.save({...data}) })

//

  

```

- product.save() là method từ mongoose.
- module.exports = mongoose.model('Product', productSchema); =>  Product sẽ được thêm vào database ở dạng số nhiều.

- Các method khác :
  + Product.find().then() : tìm tất cả items.
  + Proudct.findById(prodId).then() : tìm item theo id.
  + Product.findByIđAnRemove(prodId).then()
  + User.findOne().then(); nếu ko có id sẽ trả về user đầu tiên tìm đc.

# Using Relation 

```
  const productSchema = new Schema({
    title: {
      type: String,
      require: true
    },
    price: {
      type: Number,
      requrie: true
    },
    userId: {
      type: Schema.Types.ObjectId,
      ref: 'User',
      require: true,
    }
  });

////
  const product = new Product({ ...data, userId : req.user._id or req.user })
    
```
- ref : sẽ tạo kết nối với model User.
- req.user => mongoose sẽ tự động ấy id.

# Fethingj data through id
- lấy thêm data từ id.
```
  Product.find()
  .select('title price -_id')
  .populate('userId', 'name')
  .then(products => {})

```

- select cho ta phép lấy property trong populate.
- populate cho phép ta lấy thêm data từ field mong muốn.
- userId là 1 thuộc tính trong product.

# Thêm method vào schema

```
  userSchema.methods.addToCart = (product) => {
      this // this sẽ trỏ đến object đc tạo từ new User.
  }

```

- Sẽ thêm method cho schema

```
  req.user.populate('cart.item.productId').execPopulate().then(user => {
    const products = user.cart.items.map(i => { return { product: { ...i.productId._doc } } })
  });

```
- execPopulate cho phép ta async.
- _doc trả về cả doc của productId.

```
  Order.find({'user.userId': req.user._id}).then(orders => {});

```
