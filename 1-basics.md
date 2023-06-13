# How the web works.
- Từ url với dman lookup tìm đc ip server, user gửi request đến server, nodejs sẽ xử lý.
- Sau khi xử lý gưi lại response cho User.
- requestr , response được xử lý protocol bởi HTTP , HTTPS
- HTTP : Hyper Text Transfer Protocol : giao thức chuyển đổi data giữa Browser và Serverr.
- HTTPS : HTTP + Data Encryption trong quá trình chuyển data.

# Create a Node Server.
- Các Core Modules : http, https, fs, path, os
  + http : launch a server, send request.
  + https : Launch a SSL server.

- Tạo server : 

```
  const http = require('http');
  const reqListen = (req, res) => {
    console.log(req);
  }

  const server = http.createServer(reqListen);

  server.listen(3000);

```

- Mỗi lần request đền thì node sẽ chạy function được truyền vào.

# Node LLifecycle
- node app.js => start script => parse code, regíter variables and functions => Event Loop.
- Event Loop sẽ chạy liên tục cho tới khi có 1 event listeners được registered.
- Single threat 
- process.exit(); => tắt event loop hay tắt server.

# req object 
- req.url => url của request
- req.method => method của request.
- req.header => các thông tin header.


# res object.
- res.setHeader('Content-Type', 'text/html') : attach header vào response, nhận vào 2 params
- res.write(data) : viết data để truyền vào response.
- res.end(); : send dữ liệu cho client, ko thể thay đỏi res sau khi gọi.
- Có thể tìm hiểu thêm về các headers tại đây : https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers

```
const server = http.createServer((req, res) => {
    // console.log(req);
    res.setHeader("Content-Type", 'text/html');
    res.write(`
        <html>
            <head>
                <title>First Node app</title>
            </head>
            <body>
                <h1>Hello Node js</h1>
            </body>
        </html>
    `);
    res.end();
});



```

# Routing Requests and redirecting requests

```
  const url = req.url;
    if (url === '/') {
        res.write(`
            <h1>Hello Node js< message/h1>
        `);
        return res.end();
    }
    if (url === '/message' && method == 'POST') {
        fs.writeFileSync('message.txt', 'DUMMY Text');
        // res.writeHead(302, )
        res.statusCode = 302;
        res.setHeader('location', '/');
        return res.end();
    }

```

res.writeHead(302, headers: obj) : gửi status code và set headers.
res.statusCode = 302;
res.setHeader('location', '/');

# Parsing Request Bodies
- data được gửi đi dưới dạng stream data. 

## Streams and Buffers
- Stream cơ bản là 1 quá trình chuyển tiếp, 
- request được node read theo chunks hay multiple parts.
- Đối với file upload, streaming sẽ giúp ghi vào disk của server.

- Để làm việc, sắp xếp lại với các chunks ta sẽ dùng cái được gọi là Buffer.
- Buffer sẽ giữ và xửy lý nhiều chunks trước khi đc release.

```
  if (url === '/message' && method == 'POST') {
        const body = [];
        req.on('data', (chunk) => {
            console.log(chunk);
            body.push(chunk);
        });
        return req.on('end', () => {
            const parseBody = Buffer.concat(body).toString();
            const message = parseBody.split('=')[1];
            fs.writeFileSync('message.txt',message);
            // res.writeHead(302, )
            res.statusCode = 302;
            res.setHeader('location', '/');
            return res.end();
        });
    }

```
- req.on sẽ lắng nghe sự kiện
- sự kiện 'data' sẽ được gọi mỗi khi có 1 chunk mới đc sắn sàng đọc.
- sự kiện 'end' được gọi khi data kết thúc.

# Blocking and Non-Blocking
-fs.writeFileSync('message.txt',message); sẽ block lại cho đến khi tạo xog file.
- Nếu file lớn sẽ làm block code.
- Ta sẽ dùng fs.writeFile sẽ xử lý bát đồng bộ.

```
  if (url === '/message' && method == 'POST') {
        const body = [];
        req.on('data', (chunk) => {
            console.log(chunk);
            body.push(chunk);
        });
        return req.on('end', () => {
            const parseBody = Buffer.concat(body).toString();
            const message = parseBody.split('=')[1];
            fs.writeFile('message.txt', message, (err) => {
                res.statusCode = 302;
                res.setHeader('location', '/');
                return res.end();
            });
        });

    };

```
# Single Thread, Event Loop, Blocking Code.
- Node sử dụng Single JS Thread. => làm cách nào để xửy liều nhiều request cùng lúc.
- Event Loop sẽ start khi js programme start, chịu trách nhiệm cho handle callback của các event như createServer...
- Các tác vụ lâu, nặng như fs.write sẽ được Worker Pool xử lý.
- Worker pool cũng đc quản lý bởi nodejs. Sẽ chạy ở 1 thead khác
- Khi worker done, nó sẽ trigger eventv và kết thúc ở event loop (gọi callback);

## Event Loop
- giữ cho node chạy và xử lý callbacks theo order.
- Mỗi lần lặp , sẽ check xem có bát cứ timer callbacks nào cần execute không.
- Sau đó sẽ check các callbacks khác. Nếu có nhiều callbacks quá thì sẽ hoãn lại cho tới lần lặp event loop tiép theo.
- Sau khi xử lý hết callback, event loop sẽ vào poll pharse,  
- Poll pharse là giai đoạn mà nodejs will tìm kiếm các IO event tiếp theo.
- Sau đó vào gia đoạn check
- Tiếp đến là giai đoạn close callbacks.
- 

 











