# EJS
- Cần báo cho espress biết để render template.
- app.set cho phép ta set name - value cho app. 
- app.get để lấy value từ app.set
- name "view engine" : đối với view render nào cần render thì hãy dùng engine này để render.
- name "views" : chỉ nơi cần tìm views.
- ĐỌc thêm docs của express.

```
app.set("view engine", "ejs");
app.set("views", "views");


// shop.js
router.get("/", (req, res, next) => {
  res.render('shop' , {prods: products, docTitle: "Shop"});
});

```
