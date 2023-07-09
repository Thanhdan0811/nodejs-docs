# Multiple Part form data.
- app.use(bodyParser.urlencoded({extended: false})); => urlencoded là text data.
- urlencoded => Content-Type: application/x-ww-form-urlencoded
- bođyParser ko hỗ trợ xử lý file. Ta sẽ dùng multer.
- npm i multer
- ultipart/form-data sẽ cho server biết là vừa có text data vừa có binary data.
- .single(fileType) => single file.

```
  <form enctype="multipart/form-data"></form>

  //// app.js
  const multer = require('multer');
  app.use(multer({dest: 'images'}).single('image'));

  /// admin.js controller.
  console.log(req.file);
  /// app.use(multer().single('image'));
  => {
    fieldname: 'image',
    originalname: 'boat.png',
    encoding: '7bit',
    mimetype: 'image/png',
    buffer: <Buffer....>,
    size: 124123
  }

  /// app.use(multer({dest: 'image'}).single('image'));
  => {
    fieldname: 'image',
    originalname: 'boat.png',
    encoding: '7bit',
    mimetype: 'image/png',
    path: 'images/c312123'
    size: 124123
  }
  /// đổi tên c312123 => c312123.png sẽ hiện ra ảnh trong folder image.
  /// dest: 'image' => sẽ chuyển đổi thành file cho ta.

```

# Adjust file name and file path

```
//// app.js
const multer = require('multer');

const fileStorage = multer.diskStorage({
  destination: (req, file, cb) => {
      cb(null, 'images');
  } ,
  filename: (req, file, cb) => {
      // cb(null, file.filename + "-" + file.originalname);
      cb(null, new Date().toISOString() + "-" + file.originalname);
  }
});

app.use(multer({storage: fileStorage}).single('image'));

```

# Filter file by mimetype

```
const fileFIlter = (req, file, cb) => {
  if(file.mimetype === 'image/png' || file.mimetype === 'image/jpg' || file.mimetype === 'image/jpeg' ) {
    cb(null, true);
  } else {
    cb(null, false);
  }
}

app.use(multer({storage: fileStorage, fileFilter: fileFilter}).single('image'));

```

# Storing in Database.
- Ta ko nên lưu trong database mà lưu path file dẫn đến file trong source.

```
const imageUrl = image.path;


```

# Served static file : 
```
/// app.js => express.static(path.join(__dirname, 'images') => xem folder images là staic
/// images/nae_1.png => từ thư mục static là images/images tìm file có tên nae_1/png => lỗi. Ta sẽ thêm path /images ở trước.
app.use('/images', express.static(path.join(__dirname, 'images')));

/// ejs file ; product.imageUrl => images/nae_1.png
<image src="/<%= product.imageUrl  %>" />

```

# Download file with authentication 

```
/// shop.js controller getInvoice.
const orderId = req.params.orderId;
Order.fincById(orderId).then(order => {
  if(!order) {
    return next(new Error("No order found"));
  }
  if(order.user.userId.toString() !== req.user._id.toString()) {
      return next(new Error("Unauthoized"));
  }
})
const invoiceName = 'invoice-' + orderId + '.pdf';
const invoicePath = path.join('data', 'invoices', invoiceName);
fs.readFile(invoicePath, (err, data) => {
  if(err) {
    return next(err);
  }
  res.setHeader('Content-Type', 'application/pdf');
  res.setHeader('Content-Disposition', 'inline; filename="' + invoiceName + '"');
  res.send(data);
});
```
- inline (open inline) or attachment  .
- Content-Disposition : diễn tả cách mà content đc server đối với client.

# Streaming data and Preloading Data.

```
/// shop.js controller getInvoice.
const orderId = req.params.orderId;
Order.fincById(orderId).then(order => {
  if(!order) {
    return next(new Error("No order found"));
  }
  if(order.user.userId.toString() !== req.user._id.toString()) {
      return next(new Error("Unauthoized"));
  }
})
const invoiceName = 'invoice-' + orderId + '.pdf';
const invoicePath = path.join('data', 'invoices', invoiceName);
const file = fs.createReadStream(invoicePath);
res.setHeader('Content-Type', 'application/pdf');
res.setHeader('Content-Disposition', 'inline; filename="' + invoiceName + '"');
res.send(data);
file.pipe(res);

```
- createReadStream tạo stream data thay vì tải file nặng

# using PDFKit for .pdf generation.
- npm i pdfkit


```
/// Shop.js
const pdfkit = require('pdfkit');

///
const orderId = req.params.orderId;
Order.fincById(orderId).then(order => {
  if(!order) {
    return next(new Error("No order found"));
  }
  if(order.user.userId.toString() !== req.user._id.toString()) {
      return next(new Error("Unauthoized"));
  }
})
const invoiceName = 'invoice-' + orderId + '.pdf';
const invoicePath = path.join('data', 'invoices', invoiceName);

const pdfDoc = new pdfkit();
res.setHeader('Content-Type', 'application/pdf');
res.setHeader('Content-Disposition', 'inline; filename="' + invoiceName + '"');
pdfDoc.pipe(fs.createWriteStream(invoicePath));
pdfDoc.pipe(res);

pdfDoc.fontSize(26).text('Invoice', {
  underline: true
});



 pdfDoc.text('============');
  let totlaPrice = 0;
order.products.forEach(prod => {
totlaPrice += prod.quantity * prod.product.price;
  pdfDoc.fontSize(14).text(prod.product.title + ' - ' + prod.quantity + ' x ' + prod.product.price);
});

pdfDoc.text('Total Price : $' + totalPrice);

pdfDoc.end();

```

# Deleting file.


```
/// tạo file.js trong util
const fs = require('fs');

const deleteFile = (filePath) => {
  fs.unlink(filePath, (err) => {
      if(err) {
        throw(err);
      }
  });
}
exports.deleteFile = deleteFile;

/// admin controllers
const fileHelper = require('.../util/file');

fileHelper.deleteFile(product.imageUrl);


```
