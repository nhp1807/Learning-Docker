# Bài 1.5
## Yêu cầu
Trong bài 1.3, ta sử dụng devopsdockeruh/simple-web-service:ubuntu
Sau đây là ứng dụng tương tự nhưng sử dụng Alpine Linux hay vì Ubuntu: devopsdockeruh/simple-web-service:alpine

Pull cả 2 image về và so sách kích thước. Vào trong container của Alpine và đảm bảo chức năng tin nhắn bí mật giống nhau

Pull image mới về

```
$ docker run devopsdockeruh/simple-web-service:alpine
Unable to find image 'devopsdockeruh/simple-web-service:alpine' locally
alpine: Pulling from devopsdockeruh/simple-web-service
ba3557a56b15: Pull complete 
1dace236434b: Pull complete 
4f4fb700ef54: Pull complete 
Digest: sha256:dd4d367476f86b7d7579d3379fe446ae5dfce25480903fb0966fc2e5257e0543
Status: Downloaded newer image for devopsdockeruh/simple-web-service:alpine
Starting log output
Wrote text to /usr/src/app/text.log
Wrote text to /usr/src/app/text.log
Wrote text to /usr/src/app/text.log
```

Thực hiện vào container và đọc nội dung

```
$ docker exec -it gifted_wilson sh
/usr/src/app # ls
server    text.log
/usr/src/app # cat text.log 
2024-05-29 13:56:56 +0000 UTC
2024-05-29 13:56:58 +0000 UTC
2024-05-29 13:57:00 +0000 UTC
2024-05-29 13:57:02 +0000 UTC
2024-05-29 13:57:04 +0000 UTC
Secret message is: 'You can find the source code here: https://github.com/docker-hy'
```

# Bài 1.6
## Yêu cầu
Thực hiện câu lệnh: docker run -it devopsdockeruh/pull_exercise

Điều hướng đến Docker hub để tìm docs và Dockerfile sử dụng để tạo ra image
Đọc Dockerfile hoặc docs để đọc input là gì và sẽ nhận được "secret message"

```
$ docker run -it devopsdockeruh/pull_exercise
Unable to find image 'devopsdockeruh/pull_exercise:latest' locally
latest: Pulling from devopsdockeruh/pull_exercise
8e402f1a9c57: Pull complete 
5e2195587d10: Pull complete 
6f595b2fc66d: Pull complete 
165f32bf4e94: Pull complete 
67c4f504c224: Pull complete 
Digest: sha256:7c0635934049afb9ca0481fb6a58b16100f990a0d62c8665b9cfb5c9ada8a99f
Status: Downloaded newer image for devopsdockeruh/pull_exercise:latest
Give me the password: basics
You found the correct password. Secret message is:
"This is the secret message"
```

# Bài 1.7
## Yêu cầu
Tạo file script.sh trên máy local và nội dung ở trong là

```
while true
do
  echo "Input website:"
  read website; echo "Searching.."
  sleep 1; curl http://$website
done
```

Tạo 1 Dockerfile để build 1 image mới sử dụng ubuntu:22.04 và thêm hướng dẫn để tải curl vào image đó. Sau đó thêm hướng dẫn để copy file script vào image, cuối cùng chạy container sử dụng CMD
Sau khi tạo ra Dockerfile, build image với tên là "curler"

- Nếu có lỗi permission denied, sử dụng chmod

Đầu ra mong muốn

```
$ docker run -it curler

  Input website:
  helsinki.fi
  Searching..
  <!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
  <html><head>
  <title>301 Moved Permanently</title>
  </head><body>
  <h1>Moved Permanently</h1>
  <p>The document has moved <a href="https://www.helsinki.fi/">here</a>.</p>
  </body></html>
```

## Bài làm

Tạo file script.sh và đưa nội dung vào file

```
$ touch script.sh
$ nano script.sh
```

Sửa nội dung Dockerfile

```
# Start from the alpine image that is smaller but no fancy tools
FROM ubuntu:22.04

# Use /usr/src/app as our workdir. The following instructions will be executed in this location.
WORKDIR /usr/src/app

# Copy the script.sh file from this directory to /usr/src/app/ creating /usr/src/app/hello.sh
COPY script.sh .

# Alternatively, if we skipped chmod earlier, we can add execution permissions during the build.
RUN chmod +x script.sh

# Install curl
RUN apt update && apt upgrade && apt install -y curl

# When running docker run the command will be ./hello.sh
CMD ./script.sh

```

Build image từ Dockerfile và đặt tên là curler

```
$ docker build . -t curler
[+] Building 86.1s (10/10) FINISHED                                                                                                                      docker:default
 => [internal] load build definition from Dockerfile                                                                                                               0.0s
 => => transferring dockerfile: 617B                                                                                                                               0.0s
 => [internal] load metadata for docker.io/library/ubuntu:22.04                                                                                                    3.4s
 => [internal] load .dockerignore                                                                                                                                  0.0s
 => => transferring context: 2B                                                                                                                                    0.0s
 => [1/5] FROM docker.io/library/ubuntu:22.04@sha256:a6d2b38300ce017add71440577d5b0a90460d0e57fd7aec21dd0d1b0761bbfb2                                             17.2s
 => => resolve docker.io/library/ubuntu:22.04@sha256:a6d2b38300ce017add71440577d5b0a90460d0e57fd7aec21dd0d1b0761bbfb2                                              0.0s
 => => sha256:a6d2b38300ce017add71440577d5b0a90460d0e57fd7aec21dd0d1b0761bbfb2 1.13kB / 1.13kB                                                                     0.0s
 => => sha256:2af372c1e2645779643284c7dc38775e3dbbc417b2d784a27c5a9eb784014fb8 424B / 424B                                                                         0.0s
 => => sha256:52882761a72a60649edff9a2478835325d084fb640ea32a975e29e12a012025f 2.30kB / 2.30kB                                                                     0.0s
 => => sha256:a8b1c5f80c2d2a757adc963e3fe2dad0b4d229f83df3349fbb70e4d12dd48822 29.53MB / 29.53MB                                                                  15.1s
 => => extracting sha256:a8b1c5f80c2d2a757adc963e3fe2dad0b4d229f83df3349fbb70e4d12dd48822                                                                          1.7s
 => [internal] load build context                                                                                                                                  0.1s
 => => transferring context: 146B                                                                                                                                  0.0s
 => [2/5] WORKDIR /usr/src/app                                                                                                                                     0.3s
 => [3/5] COPY script.sh .                                                                                                                                         0.1s
 => [4/5] RUN chmod +x script.sh                                                                                                                                   0.4s
 => [5/5] RUN apt update && apt upgrade && apt install -y curl                                                                                                    63.8s
 => exporting to image                                                                                                                                             0.6s 
 => => exporting layers                                                                                                                                            0.5s 
 => => writing image sha256:86c673efd3d662cb5fa12b9a8977b3b12ac11c544470f7da876433d29ef061d5                                                                       0.0s 
 => => naming to docker.io/library/curl
```

```
$ docker run -it curler
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

# Bài 1.8
# Yêu cầu
Mặc định, devopsdockeruh/simple-web-service:alpine sẽ không có CMD. Thay vào đó, sử dụng ENTRYPOINT để định nghĩa hàm chạy

Như bạn có thể thấy, nó không khởi động web service mặc dù tên là "simple-web-service". Cần có một đối số phù hợp để khởi động máy chủ

Thử gõ lệnh docker run devopsdockeruh/simple-web-service:alpine hello. Chương trình sẽ đọc đối số "hello"

Trong bài tập này, ta sẽ tạo 1 Dockerfile và sử dụng FROM và CMD để tạo image mới và tự động khởi chạy server

Tài liệu Docker CMD nói một cách gián tiếp rằng nếu một image được xác định ENTRYPOINT, thì CMD sẽ được sử dụng để xác định các đối số mặc định đó.

Tag cho image mới là "web-server"

Trả về Dockerfile và lệnh bạn đã sử dụng để chạy container

Chạy image "web-service" được xây dựng sẽ trông như thế này:

```
$ docker run web-server
[GIN-debug] [WARNING] Creating an Engine instance with the Logger and Recovery middleware already attached.

[GIN-debug] [WARNING] Running in "debug" mode. Switch to "release" mode in production.
- using env:   export GIN_MODE=release
- using code:  gin.SetMode(gin.ReleaseMode)

[GIN-debug] GET    /*path                    --> server.Start.func1 (3 handlers)
[GIN-debug] Listening and serving HTTP on :8080
```

## Bài làm
Nội dung Dockerfile sau khi sửa

```
# Use the specified image as the base
FROM devopsdockeruh/simple-web-service:alpine

# Set the default command to run the server
CMD ["server"]
```

Thực thi chương trình như đã làm