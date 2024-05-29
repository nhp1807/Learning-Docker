# Running and stopping containers

Khởi chạy container ubuntu

```
$ docker run ubuntu
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
49b384cc7b4a: Pull complete 
Digest: sha256:3f85b7caad41a95462cf5b787d8a04604c8262cdcdf9a472b8c52ef83375fe15
Status: Downloaded newer image for ubuntu:latest
```

Có vẻ như không có gì xảy ra. Thực ra nó đã cố mở một shell nhưng chúng ta sẽ cần thêm một vài flag để tương tác với nó.

```
$ docker run -t ubuntu
root@33b70b1e658b:/# 
```

Bây giờ, chúng ta đã ở trong container, nhưng nếu ta thử gõ lệnh ls, sẽ không có gì xảy ra. Bởi vì terminal không gửi tin nhắn vào container. Tham số -i sẽ hướng dẫn chuyển STDIN vào vùng chứa.

```
$ docker run -it ubuntu
root@0bc71e3ca027:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

Hãy thử chạy 1 số thứ ở dưới background

```
$ docker run -d -it --name looper ubuntu sh -c 'while true; do date; sleep 1; done'
3b45a25698697b1a91adef27a8ec14bdf265eea94099293cf7aacbfa9db7a395
```

Tiếp theo, ta hãy theo dõi log của container vừa chạy
```
$ docker logs -f looper
Wed May 29 04:00:19 UTC 2024
Wed May 29 04:00:20 UTC 2024
Wed May 29 04:00:21 UTC 2024
Wed May 29 04:00:22 UTC 2024
Wed May 29 04:00:23 UTC 2024
Wed May 29 04:00:24 UTC 2024
```

Chúng ta có thể dừng 1 container bằng câu lệnh docker pause looper và sử dụng docker unpause looper để tiếp tục

Tiếp theo ta sử dụng docker attach looper trên 1 terminal khác, ta sẽ thấy log sẽ được in ra ở cả 2 terminal

## Chạy 1 process trong container bằng câu lệnh exec

Ví dụ

```
$ docker exec looper ls -la
total 56
drwxr-xr-x   1 root root 4096 May 29 04:00 .
drwxr-xr-x   1 root root 4096 May 29 04:00 ..
-rwxr-xr-x   1 root root    0 May 29 04:00 .dockerenv
lrwxrwxrwx   1 root root    7 Apr 22 13:08 bin -> usr/bin
drwxr-xr-x   2 root root 4096 Apr 22 13:08 boot
drwxr-xr-x   5 root root  360 May 29 04:07 dev
drwxr-xr-x   1 root root 4096 May 29 04:00 etc
drwxr-xr-x   3 root root 4096 Apr 29 14:05 home
lrwxrwxrwx   1 root root    7 Apr 22 13:08 lib -> usr/lib
lrwxrwxrwx   1 root root    9 Apr 22 13:08 lib64 -> usr/lib64
drwxr-xr-x   2 root root 4096 Apr 29 14:02 media
drwxr-xr-x   2 root root 4096 Apr 29 14:02 mnt
drwxr-xr-x   2 root root 4096 Apr 29 14:02 opt
dr-xr-xr-x 545 root root    0 May 29 04:07 proc
drwx------   2 root root 4096 Apr 29 14:05 root
drwxr-xr-x   4 root root 4096 Apr 29 14:05 run
lrwxrwxrwx   1 root root    8 Apr 22 13:08 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 Apr 29 14:02 srv
dr-xr-xr-x  14 root root    0 May 29 04:07 sys
drwxrwxrwt   2 root root 4096 Apr 29 14:05 tmp
drwxr-xr-x  12 root root 4096 Apr 29 14:02 usr
drwxr-xr-x  11 root root 4096 Apr 29 14:05 var
```

Chúng ta có thể thực thi bash shell trong container

```
$ docker exec -it looper bash
root@3b45a2569869:/# ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.0   2800  1052 pts/0    Ss+  04:07   0:00 sh -c while true; do date; sleep 1; done
root         399  0.2  0.0   4588  3860 pts/1    Ss   04:10   0:00 bash
root         424  0.0  0.0   2696  1020 pts/0    S+   04:10   0:00 sleep 1
root         425  0.0  0.0   7888  3976 pts/1    R+   04:10   0:00 ps aux
```

Hãy bắt đầu một quá trình khác với -it và thêm --rm để tự động xóa nó sau khi nó thoát. --rm đảm bảo rằng không có garbage container nào bị bỏ lại. Điều đó cũng có nghĩa là không thể sử dụng docker start để khởi động container sau khi nó đã thoát.