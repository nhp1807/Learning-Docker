# Defining start conditions for the container
Tiếp theo, chúng ta sẽ bắt đầu hướng tới một image có ý nghĩa hơn. yt-dlp là chương trình tải xuống video YouTube và Imgur. Hãy thêm nó vào một image - nhưng lần này, chúng ta sẽ thay đổi quy trình của mình. Thay vì quy trình hiện tại là thêm mọi thứ vào Dockerfile và hy vọng nó hoạt động, hãy thử một cách tiếp cận khác. Lần này chúng ta sẽ mở một phiên tương tác và kiểm tra nội dung trước khi "lưu trữ" nó trong Dockerfile của mình.

```
$ docker run -it ubuntu:22.04
root@c15e7ff7c60c:/# curl -L https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp -o /usr/local/bin/yt-dlp
```

Nếu curl chưa được tải, hãy tải curl trước

Tiếp theo, ta cấp quyền cho file đã download

```
$ chmod a+rx /usr/local/bin/yt-dlp
$ yt-dlp
/usr/bin/env: 'python3': No such file or directory
```
Ta cần download python

```
apt-get install -y python3
```
Sau đó, ta có thể chạy thử lại chương trình

```
$ yt-dlp

Usage: yt-dlp [OPTIONS] URL [URL...]

yt-dlp: error: You must provide at least one URL.
Type yt-dlp --help to see a list of all options.
```

Từ ubuntu:22.04, ta có thể thêm các bước trên vào trong Dockerfile. Ta cần thêm WORKDIR để đảm bảo video được download vào đó

```
FROM ubuntu:22.04

WORKDIR /mydir

RUN apt-get update && apt-get install -y curl python3
RUN curl -L https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp -o /usr/local/bin/yt-dlp
RUN chmod a+x /usr/local/bin/yt-dlp

CMD ["/usr/local/bin/yt-dlp"]
```

Build Dockerfile và chạy nó

```
$ docker build -t yt-dlp .
...

$ docker run yt-dlp

Usage: yt-dlp [OPTIONS] URL [URL...]

yt-dlp: error: You must provide at least one URL.
Type yt-dlp --help to see a list of all options.
```

Cách tự nhiên để sử dụng hình ảnh này là đưa URL làm đối số nhưng thật không may, nó không hoạt động:

```
$ docker run yt-dlp https://www.youtube.com/watch?v=uTZSILGTskA

docker: Error response from daemon: failed to create task for container: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "https://www.youtube.com/watch?v=uTZSILGTskA": stat https://www.youtube.com/watch?v=uTZSILGTskA: no such file or directory: unknown.
ERRO[0000] error waiting for container: context canceled
```

Như chúng ta đã biết, đối số mà chúng tôi đưa ra sẽ thay thế lệnh hoặc CMD

Chúng ta cần một cách để có một cái gì đó trước lệnh. May mắn thay, chúng ta có một cách để thực hiện việc này: chúng ta có thể sử dụng ENTRYPOINT để xác định tệp thực thi chính và sau đó Docker sẽ kết hợp các đối số chạy của chúng ta cho nó.

Nội dung Dockerfile cần thay đổi thành

```
FROM ubuntu:22.04

WORKDIR /mydir

RUN apt-get update && apt-get install -y curl python3
RUN curl -L https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp -o /usr/local/bin/yt-dlp
RUN chmod a+x /usr/local/bin/yt-dlp

# Replacing CMD with ENTRYPOINT
ENTRYPOINT ["/usr/local/bin/yt-dlp"]
```

Sau đó, ta thực hiện lại câu lệnh

```
$ docker run yt-dlp https://www.youtube.com/watch?v=XsqlHHTGQrw
[youtube] Extracting URL: https://www.youtube.com/watch?v=XsqlHHTGQrw
[youtube] XsqlHHTGQrw: Downloading webpage
[youtube] XsqlHHTGQrw: Downloading ios player API JSON
[youtube] XsqlHHTGQrw: Downloading player bc657243
[youtube] XsqlHHTGQrw: Downloading m3u8 information
WARNING: ffmpeg not found. The downloaded format may not be the best available. Installing ffmpeg is strongly recommended: https://github.com/yt-dlp/yt-dlp#dependencies
[info] XsqlHHTGQrw: Downloading 1 format(s): 18
[download] Destination: Master’s Programme in Computer Science ｜ University of Helsinki [XsqlHHTGQrw].mp4
[download] 100% of   13.88MiB in 00:00:15 at 893.60KiB/s
```

Sử dụng ENTRYPOINT, docker run thực thi kết hợp /usr/local/bin/yt-dlp https://www.youtube.com/watch?v=uTZSILGTskA trong container

ENTRYPOINT và CMD có thể gây nhầm lẫn - trong một image được thiết lập đúng cách, chẳng hạn như yt-dlp của chúng ta, lệnh này sẽ biểu thị danh sách đối số cho điểm vào. Theo mặc định, điểm vào trong Docker được đặt là /bin/sh -c và điều này sẽ được bỏ qua nếu không có điểm vào nào được đặt. Đây là lý do tại sao việc cung cấp đường dẫn đến tệp tập lệnh khi CMD hoạt động: bạn đang cung cấp tệp dưới dạng tham số cho/bin/sh -c.

Nếu một image định nghĩa cả hai thì CMD sẽ được sử dụng để cung cấp các đối số mặc định cho điểm vào. Bây giờ chúng ta thêm CMD vào Dockerfile:

```
FROM ubuntu:22.04

WORKDIR /mydir

RUN apt-get update && apt-get install -y curl python3
RUN curl -L https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp -o /usr/local/bin/yt-dlp
RUN chmod a+x /usr/local/bin/yt-dlp

ENTRYPOINT ["/usr/local/bin/yt-dlp"]

# define a default argument
CMD ["https://www.youtube.com/watch?v=Aa55RKWZxxI"]
```

```
$ docker run yt-dlp
[youtube] Extracting URL: https://www.youtube.com/watch?v=Aa55RKWZxxI
[youtube] Aa55RKWZxxI: Downloading webpage
[youtube] Aa55RKWZxxI: Downloading ios player API JSON
[youtube] Aa55RKWZxxI: Downloading player bc657243
[youtube] Aa55RKWZxxI: Downloading m3u8 information
WARNING: ffmpeg not found. The downloaded format may not be the best available. Installing ffmpeg is strongly recommended: https://github.com/yt-dlp/yt-dlp#dependencies
[info] Aa55RKWZxxI: Downloading 1 format(s): 22
[download] Destination: Linus Torvalds thinks Java is a horrible language [Aa55RKWZxxI].mp4
[download] 100% of    5.60MiB in 00:00:04 at 1.30MiB/s
```

Khi ta đưa thêm 1 tham số vào lệnh run, nó sẽ ghi đè vào tham số mặc định trong Dockerfile

Có hai cách để đặt ENTRYPOINT và CMD: exec form và shell form. Chúng ta đang sử dụng exec form trong đó lệnh được thực thi. 

Ở dạng shell, lệnh được thực thi được gói bằng /bin/sh -c - nó hữu ích khi bạn cần đánh giá các biến môi trường trong lệnh như $MYSQL_PASSWORD hoặc tương tự.

Ở dạng shell, lệnh được cung cấp dưới dạng một chuỗi không có dấu ngoặc. Ở dạng thực thi, lệnh và các đối số của nó được cung cấp dưới dạng danh sách (có dấu ngoặc)

Tải file từ docker về máy local

```
docker cp "71cb1c77918f:/mydir/Linus Torvalds thinks Java is a horrible language [Aa55RKWZxxI].mp4" .
Successfully copied 5.88MB to /home/nhp1807/Documents/Docker/Devops-with-docker/Part 1/4. Defining start conditions for the container/.
```