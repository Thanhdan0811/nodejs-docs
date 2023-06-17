# Add ID to path
- Trong file routes shop : 

```
router.get('/delete');
router.get('/products/:productId');

```

- route delete cần đặt trên route :productId , nếu đặt dưới thì sẽ ko bao h lấy đc.
- Trong file controller : 

```
exports.getProduct = (req, res, next) => {
  const prodId = req.params.productId;
}

```


- Chú ý khi dùng ejs :

```
              <% for (let product of prods) { %>
                    <article class="card product-item">
                        <header class="card__header">
                            <h1 class="product__title"><%= product.title %></h1>
                        </header>
                        <div class="card__image">
                            <img src="<%= product.imageUrl %>"
                                alt="<%= product.title %>">
                        </div>
                        <div class="card__content">
                            <h2 class="product__price">$<%= product.price %></h2>
                            <p class="product__description"><%= product.description %></p>
                        </div>
                        <div class="card__actions">
                            <%- include("../includes/add-to-card.ejs", {product})%>
                        </div>
                    </article>
                <% } %>

```

- product trong for sẽ là biến của for không thể truyền xuống file của inclue.
- Ta sẽ cần truyền tham số vào include như ví dụ trên.

# Query Params path 

- Đối với path có dạng : /admin/edit-product/12412?edit=true
- ta sẽ dùng req.query.edit

```

exports.getEditProduct = (req, res, next) => {
  // edit-product/42123?edit=true
  const editMode = req.query.edit;
  if (!editMode) {
    res.redirect("/");
  }
  res.render('admin/edit-product', { 
    pageTitle: 'Edit Product',
    path: '/admin/edit-product',
    editing: editMode,
  });
};

// trong file routes

router.get('/edit-product/:productId', adminController.getEditProduct);

```


