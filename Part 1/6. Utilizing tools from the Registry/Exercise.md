# Bài 1.11
## Yêu cầu
Tạo 1 Dockerfile cho 1 phiên bản Java Spring boot cũ https://github.com/docker-hy/material-applications/tree/main/spring-example-project

Setup theo hướng dẫn của README

## Bài làm
Đầu tiên, ta cần tạo Dockerfile trong thư mục project

```
# Use Amazon Corretto 8 as the base image
FROM amazoncorretto:8

# Set the working directory
WORKDIR /usr/src/app

# Copy the Maven wrapper and the pom.xml to the container
COPY . .

# Install Maven dependencies and build the project
RUN ./mvnw package

# Expose port 8080
EXPOSE 8080

# Command to run the application
CMD ["java", "-jar", "./target/docker-example-1.1.3.jar"]
```

Sau khi có được Dockerfile, thực hiện build image và run

```
$ docker build . -t spring-project && docker run -p 8080:8080 spring-project
```

Sau khi run thành công, terminal sẽ hiển thị

```
$ docker build . -t spring-project && docker run -p 8080:8080 spring-project
[+] Building 2.1s (9/9) FINISHED                                                                                                                                                                    docker:default
 => [internal] load build definition from Dockerfile                                                                                                                                                          0.0s
 => => transferring dockerfile: 416B                                                                                                                                                                          0.0s
 => [internal] load metadata for docker.io/library/amazoncorretto:8                                                                                                                                           1.8s
 => [internal] load .dockerignore                                                                                                                                                                             0.0s
 => => transferring context: 2B                                                                                                                                                                               0.0s
 => [1/4] FROM docker.io/library/amazoncorretto:8@sha256:a13d52e4f1be33ddf3aba4e62479a73fac96dfd494399f0d1bcf35be1cef7223                                                                                     0.0s
 => [internal] load build context                                                                                                                                                                             0.0s
 => => transferring context: 1.31kB                                                                                                                                                                           0.0s
 => CACHED [2/4] WORKDIR /usr/src/app                                                                                                                                                                         0.0s
 => CACHED [3/4] COPY . .                                                                                                                                                                                     0.0s
 => CACHED [4/4] RUN ./mvnw package                                                                                                                                                                           0.0s
 => exporting to image                                                                                                                                                                                        0.0s
 => => exporting layers                                                                                                                                                                                       0.0s
 => => writing image sha256:abe953f01678798501500a6cee9f696e185cbe224349f6c4ee2d190cda32918f                                                                                                                  0.0s
 => => naming to docker.io/library/spring-project                                                                                                                                                             0.0s

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.1.3.RELEASE)

2024-06-01 13:34:47.017  INFO 1 --- [           main] c.d.dockerexample.DemoApplication        : Starting DemoApplication v1.1.3 on f3538024991f with PID 1 (/usr/src/app/target/docker-example-1.1.3.jar started by root in /usr/src/app)
2024-06-01 13:34:47.020  INFO 1 --- [           main] c.d.dockerexample.DemoApplication        : No active profile set, falling back to default profiles: default
2024-06-01 13:34:47.975  INFO 1 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2024-06-01 13:34:48.006  INFO 1 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2024-06-01 13:34:48.007  INFO 1 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.16]
2024-06-01 13:34:48.018  INFO 1 --- [           main] o.a.catalina.core.AprLifecycleListener   : The APR based Apache Tomcat Native library which allows optimal performance in production environments was not found on the java.library.path: [/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib]
2024-06-01 13:34:48.080  INFO 1 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2024-06-01 13:34:48.081  INFO 1 --- [           main] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1021 ms
2024-06-01 13:34:48.264  INFO 1 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2024-06-01 13:34:48.393  INFO 1 --- [           main] o.s.b.a.w.s.WelcomePageHandlerMapping    : Adding welcome page template: index
2024-06-01 13:34:48.542  INFO 1 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2024-06-01 13:34:48.545  INFO 1 --- [           main] c.d.dockerexample.DemoApplication        : Started DemoApplication in 1.861 seconds (JVM running for 2.248)
```

Tiếp theo, ta truy cập vào http://localhost:8080/ để nhận được kết quả

# Bài 1.12
## Yêu cầu
Trong bài này, ta tiếp tục sử dụng code đã clone, nhưng sẽ sử dụng project example-frontend

Tạo Dockerfile cho project và sử dụng command để project chạy trong container với cổng 5000 và published để khi chương trình chạy thành công, ta có thể điều hướng đến đường dẫn http://localhost:5000 để nhận kết quả

## Bài làm

Dưới đây là Dockerfile
```
# Use an official Node.js runtime as a parent image
FROM node:14

# Set the working directory in the container
WORKDIR /usr/src/app

# Copy package.json and package-lock.json to the working directory
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application to the working directory
COPY . .

# Build the application
RUN npm run build

# Install serve globally
RUN npm install -g serve

# Make port 5000 available to the world outside this container
EXPOSE 5000

# Define the command to run the app using serve
CMD [ "serve", "-s", "-l", "5000", "build" ]
```

Sau đó, ta thực hiện build và run
```
$ docker build . -t example-frontend && docker run -p 5000:5000 example-frontend
```

Sau khi run thành công, màn hình terminal sẽ hiển thị
```
INFO  Accepting connections at http://localhost:5000                                                                                                                                                              
 HTTP  6/1/2024 3:15:11 PM 172.17.0.1 GET /
 HTTP  6/1/2024 3:15:11 PM 172.17.0.1 Returned 200 in 26 ms
 HTTP  6/1/2024 3:15:11 PM 172.17.0.1 GET /static/css/main.eaa5d75e.chunk.css
 HTTP  6/1/2024 3:15:11 PM 172.17.0.1 GET /static/js/2.43ca3586.chunk.js
 HTTP  6/1/2024 3:15:11 PM 172.17.0.1 GET /static/js/main.1be634bd.chunk.js
 HTTP  6/1/2024 3:15:11 PM 172.17.0.1 Returned 200 in 5 ms
 HTTP  6/1/2024 3:15:11 PM 172.17.0.1 Returned 200 in 5 ms
 HTTP  6/1/2024 3:15:11 PM 172.17.0.1 Returned 200 in 9 ms
 HTTP  6/1/2024 3:15:11 PM 172.17.0.1 GET /static/media/toskalogo.c0f35cf0.svg
 HTTP  6/1/2024 3:15:11 PM 172.17.0.1 Returned 200 in 1 ms
 HTTP  6/1/2024 3:15:11 PM 172.17.0.1 GET /manifest.json
 HTTP  6/1/2024 3:15:11 PM 172.17.0.1 GET /favicon.ico
 HTTP  6/1/2024 3:15:11 PM 172.17.0.1 Returned 200 in 2 ms
 HTTP  6/1/2024 3:15:11 PM 172.17.0.1 Returned 200 in 4 ms
```

Mở trình duyệt và truy cập vào đường dẫn http://localhost:5000 và nhận kết quả

# Bài 1.13
## Yêu cầu

Trong bài này, ta tiếp tục sử dụng code đã clone, nhưng sẽ sử dụng project example-backend

Tạo Dockerfile cho project và sử dụng command để project chạy trong container với cổng 8080 và published để khi chương trình chạy thành công, ta có thể điều hướng đến đường dẫn http://localhost:8080 để nhận kết quả

## Bài làm

Dưới đây là Dockerfile
```
# Use an official Golang image as the base image
FROM golang:1.16

# Set the working directory in the container
WORKDIR /usr/src/app

# Copy the Go modules manifests
COPY go.mod go.sum ./

# Download all dependencies. Dependencies will be cached if the go.mod and go.sum files are not changed
RUN go mod download

# Copy the source code into the container
COPY . .

# Build the Go app
RUN go build -o server

# Set the PORT environment variable to 8080
ENV PORT=8080

# Expose port 8080 to the outside world
EXPOSE 8080

# Command to run the executable
CMD ["./server"]
```

Tương tự như bài trên, ta thực hiện build và run

Sau đó ta truy cập vào đường dẫn http://localhost:8080/ping ddeeer nhận kết quả

# Bài 1.14
## Yêu cầu

Khởi chạy cả 2 project đã làm phía trên và thêm EVN vào Dockerfile với các thông tin cần thiết nằm trong README

Bỏ qua configuration của backend đến khi frontend gửi request đến _backend_url_/ping khi ta ấn nút

Ta biết rằng configuration đã sẵn sàng khi nút của 1.14 phía frontend phản hồi và chuyển sang màu xanh


# Bài làm
