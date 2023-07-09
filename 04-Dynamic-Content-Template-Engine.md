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


- Tại file shop.ejs

```

<%- include('./includes/head.ejs') %>
  <link rel="stylesheet" href="/css/product.css">
  </head>

  <body>
    <%- include('./includes/navigation.ejs') %>
      <main>

        <% if (prods.length> 0) { %>
          <div class="grid">
            <% for (let prod of prods) { %>
              <article class="card product-item">
                <header class="card__header">
                  <h1 class="product__title">
                    <%= prod.title %>
                  </h1>
                </header>
                <div class="card__image">
                  <img src="https://cdn.pixabay.com/photo/2016/03/31/20/51/book-1296045_960_720.png" alt="A Book">
                </div>
                <div class="card__content">
                  <h2 class="product__price">$19.99</h2>
                  <p class="product__description">A very interesting book about so many even more interesting things!
                  </p>
                </div>
                <div class="card__actions">
                  <button class="btn">Add to Cart</button>
                </div>
              </article>
              <% } %>
          </div>
          <% } else { %>
            <h1>No Products Found</h1>
            <% } %>
      </main>
      <%- include('./includes/end.ejs') %>


```
