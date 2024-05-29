# Definitions and basic concepts

## Docker là gì?
Docker là một tập hợp các sản phẩm nền tảng dưới dạng dịch vụ (PaaS) sử dụng ảo hóa cấp hệ điều hành để phân phối phần mềm trong các gói được gọi là container
- Docker là một bộ công cụ để phân phối phần mềm trong các container. 
- Container là các gói phần mềm.

## Lợi ích của containers
Sử dụng để đóng gói ứng dụng

## Chạy container
```
$ docker container run hello-world
  Unable to find image 'hello-world:latest' locally
  latest: Pulling from library/hello-world
  b8dfde127a29: Pull complete
  Digest: sha256:308866a43596e83578c7dfa15e27a73011bdd402185a84c5cd7f32a88b501a24
  Status: Downloaded newer image for hello-world:latest

  Hello from Docker!
  This message shows that your installation appears to be working correctly.

  To generate this message, Docker took the following steps:
   1. The Docker client contacted the Docker daemon.
   2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
      (amd64)
   3. The Docker daemon created a new container from that image which runs the
      executable that produces the output you are currently reading.
   4. The Docker daemon streamed that output to the Docker client, which sent it
      to your terminal.

  To try something more ambitious, you can run an Ubuntu container with:
   $ docker run -it ubuntu bash

  Share images, automate workflows, and more with a free Docker ID:
   https://hub.docker.com/

  For more examples and ideas, visit:
   https://docs.docker.com/get-started/

```

docker container run có thể viết gọn lại thành docker run

```
$ docker run hello-world

  Hello from Docker!
  ...
```

## Image và container
Container là instance của image

### Image
Docker image là 1 file và không thể thay đổi sau khi được tạo ra
Để liệt kê các image, ta có thể sử dụng câu lệnh sau:
```
$ docker image ls
REPOSITORY    TAG       IMAGE ID       CREATED       SIZE
hello-world   latest    d2c94e258dcb   13 months ago   13.3kB
```
Container được tạo bởi docker image
Image được tạo ra bởi Dockerfile

#### Dockerfile
```
FROM <image>:<tag>

RUN <install some dependencies>

CMD <command that is executed on `docker container run`>
```

### Container
Chứa những gì cần để thực thi chương trình
Để liệt kê các container, ta có thể sử dụng câu lệnh sau
```
$ docker container ls
  CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```
Tuy nhiên, câu lệnh trên chỉ hiển thị những container đang chạy, để hiển thị đầy đủ các container đang tồn tại, ta có thể thêm tham số -a vào:
```
$ docker container ls -a
CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                      PORTS     NAMES
1fff232e9da8   hello-world   "/hello"   18 minutes ago   Exited (0) 18 minutes ago             clever_brahmagupta
044f9b89ec5a   hello-world   "/hello"   21 minutes ago   Exited (0) 21 minutes ago             determined_goldberg
```
Câu lệnh docker container ls có thể viết ngắn lại thành docker ps

## Docker CLI
Chúng ta đang sử dụng dòng lệnh để tương tác với "Docker Engine" được tạo thành từ 3 phần: CLI, API REST và daemon Docker. Khi bạn chạy một lệnh, ví dụ: docker container run, đằng sau hậu trường, client sẽ gửi request thông qua API REST tới Docker daemon, nơi xử lý image, container và các tài nguyên khác.

Một trong số chúng đã quen thuộc: docker container run image_name, daemon tạo vùng chứa từ image và tải xuống image nếu nó không có sẵn.

Container chứa các thuộc tính CONTAINER ID và NAMES, giá trị của cột name sẽ được hệ thống tự sinh ra.

Chúng ta có thể xóa container bằng lệnh docker container rm, câu lệnh này có thể nhận ID hoặc NAME làm tham số truyền vào

Chúng ta còn có thể xóa hết các container bằng câu lệnh docker container prune và cũng có thể xóa hết các image bằng câu lệnh docker image prune.

Chúng ta sử dụng câu lệnh docker system prune để xóa hầu hết mọi thứ

Tiếp theo, hãy khởi tạo 1 container khác
```
$ docker run nginx
```
Sau khi thực hiện câu lệnh này, terminal sẽ đưng yên, để giải quyết hiện tượng này, ta sử dung thêm tham số -d
```
$ docker run -d nginx
```
-d ở đây nghĩa là detached mode, tức là chạy ở chế độ nền

Ta có thể kiểm tra danh sách các container đang chạy
```
$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS              PORTS     NAMES
4fedfcd84616   nginx     "/docker-entrypoint.…"   About a minute ago   Up About a minute   80/tcp    brave_booth
```
Bây giờ, ta tiến hành xóa container đó
```
$ docker container rm brave_booth
Error response from daemon: cannot remove container "/brave_booth": container is running: stop the container before removing or force remove
```
Như màn hình hiển thị, ta không thể xóa 1 container đang chạy, chúng ta cần stop và sau đó mới có thể remove
```
$ docker container stop brave_booth
$ docker container rm brave_booth
```