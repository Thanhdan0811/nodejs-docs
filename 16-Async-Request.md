# What is Async Request.
- Là khi request được gửi và có gửi kèm data ở dạng JSON.
- Response gửi lại JSON, Và không làm reload trang web.

```
/// public tạo file admin.js ở client.

// gán vào sự kiện click button delete. truyền vào this.
const deleteProduct = (btn) => {
  const prodId = btn.parentNode.querySelector('[[name=productId]').value;
  const csrf = btn.parentNode.querySelector('[[name=_csrf]').value;

  const productElement = btn.closest('article');

  fetch('/admin/product/' + prodId, {
    method: "delete",
    headers: {
      "csrf-token": csrf
    }
  })
  .then((result) => {
    return resule.json();
  })
  .then(data => {
    productElement.parentNode.removeChild(productElement);
  })
  .catch(err => {
    console.log(err);
  })
}

```

- JSON => Javascript Object Notation.

```
/// admin.js routes

router.delete('/product/:productId', isAuth, adminController.deleteProduct);

/// admin.js controllers
exports.deleteProduct = (req, res, next) => {
  ....
  res.status(200).json({message: "Success!"});
}

```
