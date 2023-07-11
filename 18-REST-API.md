# REST
- REST => Representational State Transfer.
- Chỉ khác ở response.
- Data formats :
    + HTML : ejs
    + Plain Text: No Ui Assumptions, Data
    + XML : <name>Node.js</name> => Machine-readable bute relatively verbose; XML-parser needed.
    + JSON: Can easily be converted to JS
 
# Routing and HTTP Mehod.
- API Endpoint : POST /post , GET /posts,
- Methods :
    + get : get rosource
    + post : post to resource
    + put : put a resource (create or overwrite)
    + patch : Update parts of an existing Resource
    + delete : delete a resource.
    + OPTIONS : determine whether follow-up Request is allowed.
 
# REST Principes
- Uniform Interface : Clearly defined API Endpoints with clearly defined request + response data structure.
- Stateles Interactions : Server and client dont store any connection history, every request is handled seperately.
- Cacheable : servers may set caching headers to allow the client to cache responses.
- Client-Server : seperately.
- Layered System : Server may forward requestss to other APIs
- Conde on Demand : Ececutable code may be transsferred from server to client.

# Creating REST API


```
/// app.js
const bodyParser = require('body-parser');


// app.use(bodyParser.urlencoded()) ; // x-wwww-form-urlencoded <form>
app.use(bodyParser.json()); // application/json

app.use('/feed', feedRoutes);

/// feed.js route

router.post('/post', feedController.createPost);

/// feed.js controller

exports.createPost = (req, res, next) => {
    const title = req.body.title;
    const content = req.body.content;
    res.status(201).json({
        message: 'Post created succesfully',
        post: { id: new Data().toISOString(), title: title, content }
    });
}

```

# REST API, CLients and CORS errors.
- Access-Control-Allow-Origin :
- CORS : Cross Origin Resource Sharing
- Khi client và server chạy ở 2 domain khác nhau


```
/// app.js
app.use((req, res, next) => {
    res.setHeader('Access-Control-Allow-Origin', "*");
    res.setHeader('Access-Control-Allow-Methods', "GET, POST, PUT, PATCH, DELETE");
    res.setHeader('Access-Control-Allow-Headers', 'Content-Type, Authorization');
    next();
});


/// client

fetch('', {
    method: "POST",
    body: JSON.stringify({
        title: 'A code',
        content: 'ádfádf'
    }),
    headers: {
        "Content-Type": 'application/json'
    }
})
```

- cần phải truyền kiểu json và set Content-Type ở header là application/json

# Validation
- npm i express-validator


```
/// feed.js routes

const { body } = require('express-validator/check');

router.post('/post', [
    body('title')
    .trim()
    .isLength({min: 5}),
    body('content')
    .trim()
    .isLength({min: 5}),
]);

/// feed.js controller

const { validationResult } = require('express-validator/check');

exports.createPost = (req, res, next) => {
    const errors = validationResult(req);
    if(!errors.isEmpty()) {
        return res.status(422).json({
            message: 'Validation faild',
            errors: errors.array()
        })
    }

};

```

# Mongodb and mongoose
 - npm i mongoose

```
/// app.js

const mongoose = require('mongoose');

mongoose.connect('url')
.then(result => {
    app.listen(8080);
});

/// model/post.js

const mongoose = require('mongoose');
const Schema = mongoose.Schema;

const postSchema = new Schema(
    {
        title : {
            type: String,
            required: true
        }
    },
    { timestamps: true }  // createAt: Date()
);

module.exports = mongoose.model('Post', postSchema);

```

# Static Images , Upload file.

```
/// app.js
const path = require('path');

app.use( '/images', express.static(path.join(_dirname, 'images')) );

app.use((error, req, res, next) => {
    console.log(error);
});

/// feed.js controller

const { validationResult } = require('express-validator/check');

exports.createPost = (req, res, next) => {
    const errors = validationResult(req);
    if(!errors.isEmpty()) {
       const error = new Error('Faild');
       error.statusCode = 422;
        throw error;
    }

    post.save()
    .then()
    .catch((err) => {
        if(!err.statusCode) error.statusCode = 422;
        next(err);
    })

};

```

NOTE : One important note for Windows users only:

On Windows, the file name that includes a date string is not really supported and will lead to some strange CORS errors. Adjust your code like this to avoid such errors:

nstead of
```
const storage = multer.diskStorage({
    destination: function(req, file, cb) {
        cb(null, 'images');
    },
    filename: function(req, file, cb) {
        cb(null, new Date().toISOString() + file.originalname);
    }
});

```
which we'll write in the next lecture, you should use this slightly modified version:
```
const { v4: uuidv4 } = require('uuid');
 
const storage = multer.diskStorage({
    destination: function(req, file, cb) {
        cb(null, 'images');
    },
    filename: function(req, file, cb) {
        cb(null, uuidv4())
    }
});
```
For this, install the uuid package by running:

 - npm install --save uuid

To ensure that images can be loaded correctly on the frontend, you should also change the logic in the feed.js controller:

in createPosts, change the imageUrl const:
```
exports.createPost = (req, res, next) => {
    ...
    const imageUrl = req.file.path.replace("\\" ,"/");
    ...
}
```
and in updatePost (once we added that later):
```
exports.updatePost = (req, res, next) => {
    ...
    imageUrl = req.file.path.replace("\\","/");
}
```
On macOS and Linux, you can ignore that and stick to the code I show in the videos.

- Ở FE :
```
const formData = new FormData();
formData.append('title', postData.title);
formData.append('content', postData.content);
formData.append('image', postData.image);

fetch(url, {
    method: 'POST',
    body: formData
})

```

# Updating Post.
- use put.

```
/// feed.js routes
router.put('/post/:postId');

/// feed.js controller.
exports.updatePost = (req, res, next) => {
    const postId = req.params.postId;
    const title = req.body.title;
    const content = req.body.content;
    let imageUrl = req.body.image;
    if(req.file) {
        imageUrl = req.file.path;
    }
    if(!imageUrl) {
        const error  new Error('No file picked');
        error.statusCode = 42;
        throw error;
    }
}

```
