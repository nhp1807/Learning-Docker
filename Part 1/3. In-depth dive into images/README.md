# In-depth dive into images
## Image đến từ đâu?

Khi chạy một lệnh như docker run hello-world, Docker sẽ tự động tìm kiếm hình ảnh Docker Hub nếu không tìm thấy nó ở local.

Điều này có nghĩa là chúng tôi có thể pull và chạy bất kỳ image công khai nào từ server của Docker. 

Ví dụ: nếu muốn khởi động một phiên bản của PostgreSQL, chúng ta chỉ cần chạy docker run postgres, thao tác này sẽ pull và chạy https://hub.docker.com/_/postgres/.

Chúng ta có thể tìm kiếm image trong Docker Hub bằng docker search. Hãy thử chạy docker search hello-world.

```
$ docker search hello-world
NAME                                       DESCRIPTION                                     STARS     OFFICIAL
hello-world                                Hello World! (an example of minimal Dockeriz…   2251      [OK]
rancher/hello-world                        This container image is no longer maintained…   6         
okteto/hello-world                                                                         0         
tutum/hello-world                          Image to test docker deployments. Has Apache…   90        
kitematic/hello-world-nginx                A light-weight nginx container that demonstr…   151       
thomaspoignant/hello-world-rest-json       This project is a REST hello-world API to bu…   2         
dockercloud/hello-world                    Hello World!                                    20        
ansibleplaybookbundle/hello-world-apb      An APB which deploys a sample Hello World! a…   1         
ansibleplaybookbundle/hello-world-db-apb   An APB which deploys a sample Hello World! a…   2         
crccheck/hello-world                       Hello World web server in under 2.5 MB          23        
strimzi/hello-world-consumer                                                               0         
strimzi/hello-world-producer                                                               0         
businessgeeks00/hello-world-nodejs                                                         0         
koudaiii/hello-world                                                                       0         
freddiedevops/hello-world-spring-boot                                                      0         
strimzi/hello-world-streams                                                                0         
ppc64le/hello-world                        Hello World! (an example of minimal Dockeriz…   2         
tsepotesting123/hello-world                                                                0         
garystafford/hello-world                   Simple hello-world Spring Boot service for t…   0         
infrastructureascode/hello-world           A tiny "Hello World" web server with a healt…   1         
kevindockercompany/hello-world                                                             0         
dandando/hello-world-dotnet                                                                0         
vad1mo/hello-world-rest                    A simple REST Service that echoes back all t…   7         
rsperling/hello-world3                                                                     0         
in28min/hello-world-rest-api                                                               16
```

## Chi tiết hơn về Image

Khi pull 1 image mà không gắn tag, docker sẽ tự động pull tag lastest

Ta có thể cá nhân hóa tag bằng cách đặt tag đằng sau tên tag mặc định

```
$ docker tag ubuntu:20.04 ubuntu:nhp1807
```

## Build Image
Dockerfile là 1 file bao gồm các bước để build image. Ta cần định nghĩa những gì cần có trong image với các bước khác nhau

Chúng ta cần tạo Dockerfile định nghĩa các dependencies. Ít nhất nó phụ thuộc vào thứ gì đó có thể chạy tập lệnh shell

Đầu tiên ta cần tạo 1 file và đặt tên là Dockerfile

Ở đây ta sử dụng container của image Alpine và sử dụng /usr/src/app làm thư mục để thực hành

```
$ docker start alpine_container
$ docker exec -it alpine_container
```

Taọ 1 file và đặt tên là hello.sh với nội dung là

```
#!/bin/sh
echo "Hello, docker!"
```

Chuyển file này vào thư mục làm việc trong container. Cấp quyền và chạy file đó

```
$ chmod +x hello.sh
$ ./hello.sh
  Hello, docker!
```

Tạo 1 file tên là Dockerfile và nội dung ở trong là

```
# Start from the alpine image that is smaller but no fancy tools
FROM alpine:3.19

# Use /usr/src/app as our workdir. The following instructions will be executed in this location.
WORKDIR /usr/src/app

# Copy the hello.sh file from this directory to /usr/src/app/ creating /usr/src/app/hello.sh
COPY hello.sh .

# Alternatively, if we skipped chmod earlier, we can add execution permissions during the build.
# RUN chmod +x hello.sh

# When running docker run the command will be ./hello.sh
CMD ./hello.sh
```

Sau đó sử dụng lệnh docker build.

Mặc định docker build sẽ tìm file tên là Dockerfile, nếu ta muốn chạy và đặt tên thì có thể sử dụng thêm thuộc tính -t name

```
$ docker build . -t hello-docker
[+] Building 6.0s (8/8) FINISHED                                                                                                                         docker:default
 => [internal] load build definition from Dockerfile                                                                                                               0.1s
 => => transferring dockerfile: 545B                                                                                                                               0.0s
 => [internal] load metadata for docker.io/library/alpine:3.19                                                                                                     3.4s
 => [internal] load .dockerignore                                                                                                                                  0.1s
 => => transferring context: 2B                                                                                                                                    0.0s
 => [1/3] FROM docker.io/library/alpine:3.19@sha256:c5b1261d6d3e43071626931fc004f70149baeba2c8ec672bd4f27761f8e1ad6b                                               1.7s
 => => resolve docker.io/library/alpine:3.19@sha256:c5b1261d6d3e43071626931fc004f70149baeba2c8ec672bd4f27761f8e1ad6b                                               0.1s
 => => sha256:c5b1261d6d3e43071626931fc004f70149baeba2c8ec672bd4f27761f8e1ad6b 1.64kB / 1.64kB                                                                     0.0s
 => => sha256:6457d53fb065d6f250e1504b9bc42d5b6c65941d57532c072d929dd0628977d0 528B / 528B                                                                         0.0s
 => => sha256:05455a08881ea9cf0e752bc48e61bbd71a34c029bb13df01e40e3e70e0d007bd 1.47kB / 1.47kB                                                                     0.0s
 => => sha256:4abcf20661432fb2d719aaf90656f55c287f8ca915dc1c92ec14ff61e67fbaf8 3.41MB / 3.41MB                                                                     1.2s
 => => extracting sha256:4abcf20661432fb2d719aaf90656f55c287f8ca915dc1c92ec14ff61e67fbaf8                                                                          0.1s
 => [internal] load build context                                                                                                                                  0.1s
 => => transferring context: 68B                                                                                                                                   0.0s
 => [2/3] WORKDIR /usr/src/app                                                                                                                                     0.1s
 => [3/3] COPY hello.sh .                                                                                                                                          0.1s
 => exporting to image                                                                                                                                             0.2s
 => => exporting layers                                                                                                                                            0.1s
 => => writing image sha256:9a17ba519998fa7cbc8f62d03b0a5db93dc577c2100853e4750b10cfe2871a0a                                                                       0.0s
 => => naming to docker.io/library/hello-docker
```

Sau đó ta có thể kiểm tra image vừa đặt đã có chưa

```
$ docker image ls
REPOSITORY                          TAG       IMAGE ID       CREATED          SIZE
hello-docker                        latest    9a17ba519998   32 seconds ago   7.38MB
ubuntu                              latest    bf3dc08bfed0   4 weeks ago      76.2MB
devopsdockeruh/simple-web-service   ubuntu    4e3362e907d5   3 years ago      83MB
devopsdockeruh/simple-web-service   alpine    fd312adc88e0   3 years ago      15.7MB
devopsdockeruh/simple-web-service   latest    99f995eeaa58   3 years ago      10.1MB
devopsdockeruh/pull_exercise        latest    d9854bc0e13a   5 years ago      75.3MB
```

Như ta thấy, hiện tại image có tên là hello-docker đã nhận

Cũng có thể tự tạo các lớp mới trên đầu của image. Bây giờ chúng ta tạo một file mới có tên là extra.txt và sao chép nó vào trong một thùng chứa.

Chúng ta cần 2 terminal, gọi là 1 và 2

```
# do this in terminal 1
$ docker run -it hello-docker sh
/usr/src/app #
```

Bây giờ chúng ta đang ở bên trong container. Chúng ta đã thay thế CMD mà chúng ta đã xác định trước đó bằng sh và sử dụng -i và -t để khởi động vùng chứa để chúng tôi có thể tương tác với nó.

```
# do this in terminal 2
$ docker ps
  CONTAINER ID   IMAGE          COMMAND   CREATED         STATUS         PORTS     NAMES
  9c06b95e3e85   hello-docker   "sh"      4 minutes ago   Up 4 minutes             zen_rosalind

$ touch additional.txt
$ docker cp ./additional.txt zen_rosalind:/usr/src/app/
```

Để đảm bảo file additional.txt đã có trong container, ta có thể kiểm tra bằng lệnh ls

```
/usr/src/app # ls
additional.txt  hello.sh
```

Bây giờ chúng ta đã thực hiện thay đổi đối với container. Chúng ta có thể sử dụng lệnh docker diff để kiểm tra những gì đã thay đổi

```
# do this in terminal 2
$ docker diff zen_rosalind
  C /usr
  C /usr/src
  C /usr/src/app
  A /usr/src/app/additional.txt
  C /root
  A /root/.ash_history
```

A = added, D = deleted, C = changed

additional.txt đã được tạo và tạo ra .ash_history

Tiếp theo, ta sẽ lưu lại thay đổi thành 1 image mới bawgf lệnh docker commit

```
# do this in terminal 2
$ docker commit zen_rosalind hello-docker-additional
sha256:2f63baa355ce5976bf89fe6000b92717f25dd91172aed716208e784315bfc4fd
$ docker image ls
REPOSITORY                          TAG       IMAGE ID       CREATED          SIZE
hello-docker-additional             latest    71296968b7b8   6 seconds ago    7.38MB
hello-docker                        latest    9a17ba519998   16 minutes ago   7.38MB
```

Câu lệnh docker commit sẽ tạo 1 layer mới ở trên image hello-docker, và tạo ra 1 image mới có tên là ello-docker-additional

Hãy tạo ra hello-docker với tag là v2 trong đó bao gồm file additional.txt. File mới có thể được thêm vào bằng lệnh RUN trong Dockerfile

Sửa file Dockerfile trước đó thành

```
# Start from the alpine image
FROM alpine:3.19

# Use /usr/src/app as our workdir. The following instructions will be executed in this location.
WORKDIR /usr/src/app

# Copy the hello.sh file from this location to /usr/src/app/ creating /usr/src/app/hello.sh.
COPY hello.sh .

# Execute a command with `/bin/sh -c` prefix.
RUN touch additional.txt

# When running Docker run the command will be ./hello.sh
CMD ./hello.sh
```

Bây giờ, chúng ta có thể sử dụng câu lệnh docker build . -t hello-docker:v2

```
$ docker run hello-docker-additional ls
  additional.txt
  hello.sh

$ docker run hello-docker:v2 ls
  additional.txt
  hello.sh
```