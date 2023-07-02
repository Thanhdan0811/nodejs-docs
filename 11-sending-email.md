- node server không thể tạo 1 mail server, vì nó phức tạp.
- Ta sẽ dùng bên thứ 3 mail server.
- Ta sẽ dùng sendGrid, trang web về mail server.
- ở node : npm i nodemailer nodemailer-sendgrid-transport


```
  /// auth.js
  const nodemailer = require('nodemailer');
  const sendgridTransport = require('nodemailersendgrid-transport');

  const transporter = nodemailer.createTransport(sendgridTransport({
    auth: {
      api_key: 'API_KEY'
    }
  })); 


  /// sau khi sign up
  .then(result => {
    return transporter.sendMail({
      to: email,
      from: 'shop@node-complete.com',
      subject: 'Signup succeeded',
      html: '<h1> You Sign up </h1>'
    })
  })

```
