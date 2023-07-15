# Realtime webservice.
- Server gửi message cho client mà ko cần request.
- Websocket khác với Http.
- Http established via websocket, Từ đó Server gửi data tới websocket.
- Dùng thư viện : Socket.io
- npm i socket.io

#  Set up

```
/// app.js
mongoose.connect('url')
.then(result => {
  const server = app.listen(8080);
  const io = require('socket.io')(server);
  io.on('connection', socket => {
    console.log('Client Connect')
  });
})
.catch(err => console.log(err));



```
- Ở Client : npm i socket.io-client

```
import openSocket from "socket.io=-client";

openSocet('http://localhost:8080/');

```

# Sharing IO Through file

```
/// socket.js
let io;

modudule.exports = {
  init: httpServer => {
      io = require('socket.io')(httpServer);
      return io;
  },
  getIO : () => {
    if(!io) {
      throw new Error("Socket.io not Init");
    }
  }
}

/// app.js
mongoose.connect('url')
.then(result => {
  const server = app.listen(8080);
  const io = require('./socket.io').init(server);
  io.on('connection', socket => {
    console.log('Client Connect')
  });
})
.catch(err => console.log(err));

/// feed.js
const io = require('../socket');
exports.createPost = (req, res, next) => {
  await user.save();
  io.getIO().emit('posts', {action: 'create', post: post});

/// Client side.
const socket = openSocket(url);
socket.on('posts', data => {
  if(data.action === 'create') {
    this.addPost(data.post);
  }
})
}


```
