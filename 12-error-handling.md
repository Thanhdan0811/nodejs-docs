# Error middleware

```
  .then()
  .catch(err => {
    const error = new Error(err);
    error.httpStatusCode = 500;
    return next(error);
  })


  /// app.js
  app.use((error, req, res, next) => {
    res.status(500).redirect('/500');
  })

```

- Khi ở trong catch ta phải dùng next để gửi error đi. nếu ko sẽ crash.
- ngoài then catch, nếu throw error thì app.use vẫn sẽ bắt đc, nhưng redirect sẽ load lại và có thể vòng lặp lỗi.
- nên trong app.use(err, req, res, next) ta nên render ra thay vì redirect.

# HTTP Response Codes
- 2xx => success
- 3xx => redirect
- 4xx => Client-side error.
- 5xx => Server-side error.
- https://developer.mozilla.org/en-US/docs/Web/HTTP/Status
