# Bài 1.9
## Yêu cầu
Image devopsdockeruh/simple-web-service tạo ra timestamp mỗi 2 giây và lưu vào /usr/src/app/text.log khi nó không được truyền vào command nào. Start container cùng với bind mount để logs cũng được tạo trong máy local

## Bài làm
Ta cần tạo file text.log trên máy chủ
```
$ touch text.log
```
Sau đó mount máy host với container

```
$ docker run -v ~/text.log:/usr/src/app/text.log devopsdockeruh/simple-web-service:ubuntu
Starting log output
Wrote text to /usr/src/app/text.log
Wrote text to /usr/src/app/text.log
Wrote text to /usr/src/app/text.log
Wrote text to /usr/src/app/text.log
Wrote text to /usr/src/app/text.log
Wrote text to /usr/src/app/text.log
```

Kiểm tra lại file text.log trên máy host

```
$ cat text.log 
2024-05-31 00:35:53 +0000 UTC
2024-05-31 00:35:55 +0000 UTC
2024-05-31 00:35:57 +0000 UTC
2024-05-31 00:35:59 +0000 UTC
2024-05-31 00:36:01 +0000 UTC
Secret message is: 'You can find the source code here: https://github.com/docker-hy'
2024-05-31 00:36:03 +0000 UTC
2024-05-31 00:36:05 +0000 UTC
2024-05-31 00:36:07 +0000 UTC
```

# Bài 1.10
## Yêu cầu
Trong bài tập này, chúng ta sẽ không tạo Dockerfile mới. 

Image devopsdockeruh/simple-web-service sẽ khởi động dịch vụ web ở cổng 8080 khi được cung cấp đối số "server". 

Trong Bài tập 1.8, bạn đã tạo một image có thể được sử dụng để chạy dịch vụ web mà không cần bất kỳ đối số nào. Bây giờ hãy sử dụng cờ -p để truy cập nội dung bằng trình duyệt của bạn. Đầu ra cho trình duyệt của bạn phải có dạng như sau: { message: "You connected to the following path: ...

## Bài làm
Sử dụng câu lệnh sau để khởi động image và ánh xạ port 8080 cảu máy host với port 8080 của container
```
$ docker run -p 8080:8080 web-server
[GIN-debug] [WARNING] Creating an Engine instance with the Logger and Recovery middleware already attached.

[GIN-debug] [WARNING] Running in "debug" mode. Switch to "release" mode in production.
 - using env:	export GIN_MODE=release
 - using code:	gin.SetMode(gin.ReleaseMode)

[GIN-debug] GET    /*path                    --> server.Start.func1 (3 handlers)
[GIN-debug] Listening and serving HTTP on :8080
[GIN] 2024/05/31 - 01:12:20 | 200 |      82.225µs |      172.17.0.1 | GET      "/"
[GIN] 2024/05/31 - 01:12:20 | 200 |      42.009µs |      172.17.0.1 | GET      "/favicon.ico"
```

Trên trình duyệt gõ localhost:8080 để kiểm tra kết quả đạt được
