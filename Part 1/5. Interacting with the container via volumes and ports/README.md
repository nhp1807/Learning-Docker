# Interacting with the container via volumes and ports


Quay lại với yt-dlp, nó hoạt động tốt nhưng tốn công để down video về máy local

Ta có thể sử dụng Docker volumes để lưu video vào vùng nhớ tạm bên ngoài container

Với bind mount, ta có thể nối file hoặc folder từ máy local sang container

Bắt đầu bằng việc sử dụng tham số -v, điều này cần đường dẫn tuyệt đối. Ta cần mount fodler hiện tại của mình dưới dạng /mydir vào container của mình, ghi đè mọi thứ mà chúng ta đã đặt trong thư mục đó vào Dockerfile.

```
$ docker run -v "$(pwd):/mydir" yt-dlp https://www.youtube.com/watch?v=DptFY_MszQs
```

Trong đó:

- $(pwd) là đường dẫn của server
- /mydir là đường dẫn trên container

Khi kết hợp lại, tùy chọn này sẽ ánh xạ thư mục hiện tại của máy chủ vào thư mục /mydir trong container.



Volumes đơn giản là file hoặc folder chia sẻ giữa container và máy local

Nếu 1 file bị thay đổi trong quá trình thực thi trong container, thì sự thay đổi đó cũng được lưu trên máy local kể cả khi container bị tắt

## Cho phép external connections vào containers

Một số điều cần biết:

- Gửi message: Program có thể gửi message đến URL như sau: http://127.0.0.1:3000 trong đó HTTP là giao thức, 127.0.0.1 địa chỉ IP, và 3000 là port.
- Nhận message: Program có thể được gắn lắng nghe tại port nào đó. Nếu program lắng nghe port 3000, khi có dữ liệu gửi đến port 3000 thì sẽ nhận được message

Có thể ánh xạ port của máy host tới port của container. Ví dụ: nếu bạn ánh xạ port 1000 trên máy host tới cổng 2000 trong container, sau đó bạn gửi message đến http://localhost:1000 trên máy tính của mình thì container sẽ nhận được thông báo đó nếu nó đang nghe cổng 2000 của nó.

Mở kết nối bên ngoài cho Docker container trải qua 2 bước:

- Exposing port
- Publishing port

Exposing container port là bảo container lắng nghe tới 1 port

Publishing là ánh xạ port của máy host và port của container

Để expose, sử dụng EXPOSE port trong Dockerfile

Để publish, run container với câu lệnh sau: -p host-port:container-port

Nếu bạn bỏ qua port máy host và chỉ chỉ định port container, Docker sẽ tự động chọn một port miễn phí làm port máy host:

```
$ docker run -p 4567 app-in-port
```
