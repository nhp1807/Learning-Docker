# Bài 1.3
## Yêu cầu
Image devopsdockeruh/simple-web-service:ubuntu sẽ khởi động một container xuất nhật ký thành một tệp. 

Đi vào bên trong container đang chạy và sử dụng tail -f ./text.log để theo dõi nhật ký. Cứ sau 10 giây đồng hồ sẽ gửi cho bạn một "tin nhắn bí mật".

Khởi chạy container

```
$ docker run -it devopsdockeruh/simple-web-service:ubuntu
```

Thực thi câu lệnh tail -f ./text.log

```
$ docker exec bf6ccc6b6154 tail -f ./text.log
2024-05-29 04:25:02 +0000 UTC
2024-05-29 04:25:04 +0000 UTC
2024-05-29 04:25:06 +0000 UTC
2024-05-29 04:25:08 +0000 UTC
Secret message is: 'You can find the source code here: https://github.com/docker-hy'
2024-05-29 04:25:10 +0000 UTC
2024-05-29 04:25:12 +0000 UTC
2024-05-29 04:25:14 +0000 UTC
2024-05-29 04:25:16 +0000 UTC
2024-05-29 04:25:18 +0000 UTC
Secret message is: 'You can find the source code here: https://github.com/docker-hy'
2024-05-29 04:25:20 +0000 UTC
2024-05-29 04:25:22 +0000 UTC
```
# Bài 1.4
## Yêu cầu
Khởi động ubuntu image với tiến trình sau: h -c 'while true; do echo "Input website:"; read website; echo "Searching.."; sleep 1; curl http://$website; done'

Khởi chạy 1 container trên image ubuntu

```
$ docker run -it ubuntu
```
Tải curl nếu cần

```
$ apt update
$ apt upgrade
$ apt install curl
```

Thực thi tiến trình

```
root@55a2fa6b8e72:/# sh -c 'while true; do echo "Input website:"; read website; echo "Searching.."; sleep 1; curl http://$website; done'
```

Kết quả nhận được

```
Input website:
helsinki.fi
Searching..
<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx/1.22.1</center>
</body>
</html>
```