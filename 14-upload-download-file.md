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

