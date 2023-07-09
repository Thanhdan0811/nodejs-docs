# How payments works
- Payment process : collect Payment method => verify payment method => Charge Payment method
- => Manage Payments => Process Order in App.
- Việc xử lý phức tạp.
- Ta sẽ dùng Stripe.
- Ở client gửi thông tin lên Stripe Server=> trả lại token => Server xử lý => gửi đến Stripe để thực hiện payment.
- npm i stripe


```
const stripe = require('stripe')('pk_test_API_KEY');

exports.getCheckout = (req, res, next) => {
  let products;
  let total = 0;

  req.user
    .populate('cart.items.productId')
    .execPopulate()
    .then(user => {
      const products = user.cart.items;
      let total = 0;
      products.forEach(p => {
        total += p.quantity * p.productId.price;
      });

      return stripe.checkout.sessions.create({
        payment_method_types: ['card'],
        line_items: products.map(p => {
            return {
              name: p.product.title,
              description: p.product.description,
              currency: 'usd',
              quantity: p.quantity
            }
        }).
        success_url: req.protocol + '://' + req.get('host') + '/checkout/success',
        cancel_url:  req.protocol + '://' + req.get('host') + '/checkout/cancel'
      });

    })
    .then(session => {

      res.render('shop/checkout', {
        path: '/checkout',
        pageTitle: 'Checkout',
        products: products,
        totalSum : total,
        sessionId : session.id
      });
    })
}

```

# Cách dùng  Stripe
- Vào trang web đk tk và xác nhân.
- gắn file script vào.

```
  <script src="https/js.stripe.com/v3/"></script>
  <script>
      const stripe = Stripe('pk_test_API_KEY');
      const orderBtn = document.getElementById('order-btn');
      orderBtn.addEventListener('click', function() {
        stripe.redirectToCheckout({
          sessionId: <%= sessionId %>
        });
      });
  </script>

```
  
