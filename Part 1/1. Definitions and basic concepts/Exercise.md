# Bài 1.1

## Yêu cầu
Khởi chạy 3 container từ 1 image
Stop 2 container và để 1 container còn chạy
Sử dụng lệnh docker ps -a để kiểm tra

### Khởi tạo 3 container

```
$ docker run -d nginx
81e905fe3734faece10700c3c76ccd2f316736ae0b2ea48286c1fa8803aa7cf0
$ docker run -d nginx
368fb6ba8dfb029d5c58f64bd12a160e0c444b6f862ca065275e6f59ed816379
$ docker run -d nginx
b1ecd283bcf9c4890a6c5119ffefe1ef9e83a2670632a9170993140074d2ee49
```

Kiểm tra danh sách container

```
docker ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
b1ecd283bcf9   nginx     "/docker-entrypoint.…"   3 minutes ago   Up 3 minutes   80/tcp    sleepy_hoover
368fb6ba8dfb   nginx     "/docker-entrypoint.…"   3 minutes ago   Up 3 minutes   80/tcp    elastic_diffie
81e905fe3734   nginx     "/docker-entrypoint.…"   3 minutes ago   Up 3 minutes   80/tcp    sharp_nash
```

Stop 2 container

```
$ docker container stop b1ecd283bcf9
b1ecd283bcf9
$ docker container stop 368fb6ba8dfb
368fb6ba8dfb
```

Kiểm tra lại danh sách các container đang chạy

```
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS                      PORTS     NAMES
b1ecd283bcf9   nginx     "/docker-entrypoint.…"   5 minutes ago   Exited (0) 40 seconds ago             sleepy_hoover
368fb6ba8dfb   nginx     "/docker-entrypoint.…"   5 minutes ago   Exited (0) 24 seconds ago             elastic_diffie
81e905fe3734   nginx     "/docker-entrypoint.…"   5 minutes ago   Up 5 minutes                80/tcp    sharp_nash
```

# Bài 1.2
## Yêu cầu
Xóa hết các container và image

```
$ docker container prune
WARNING! This will remove all stopped containers.
Are you sure you want to continue? [y/N] y
Deleted Containers:
b1ecd283bcf9c4890a6c5119ffefe1ef9e83a2670632a9170993140074d2ee49
368fb6ba8dfb029d5c58f64bd12a160e0c444b6f862ca065275e6f59ed816379
81e905fe3734faece10700c3c76ccd2f316736ae0b2ea48286c1fa8803aa7cf0

Total reclaimed space: 3.279kB
```

Xóa image nginx
```
$ docker image remove nginx
Untagged: nginx:latest
Untagged: nginx@sha256:a484819eb60211f5299034ac80f6a681b06f89e65866ce91f356ed7c72af059c
Deleted: sha256:e784f4560448b14a66f55c26e1b4dad2c2877cc73d001b7cd0b18e24a700a070
Deleted: sha256:38ecd4de01b55beb32c596678b061db27f8ecb586096f031e4553869e52a1dc2
Deleted: sha256:168e1116c229abdf6cd9c0f07f82d40d53e358b1da4e1242a15101ece28ccb28
Deleted: sha256:0b7dc712f354a2312c1f1bb6380d8e23919baf0cc09f32a5a4595afb6c3a440b
Deleted: sha256:3ecb32a94af1f9a46cc259bf6cc677acd1fef2023f746e973862d1a8c9e31fe3
Deleted: sha256:b4c8fa0ae3e9f4f7d0ee49a9ac13d8aebd6f2d4a53e204e75f74e4bcb143132f
Deleted: sha256:cd5b03306052e1a435b98a34cd2579dc48f8f0ca280a147f19f066ddb6a0b81d
Deleted: sha256:5d4427064ecc46e3c2add169e9b5eafc7ed2be7861081ec925938ab628ac0e25
```

Kiểm tra lại
```
$ docker image ls
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```