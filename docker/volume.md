# Volume 관련

<br>

## volume overwrite

```shell
# volume 생성
$ docker volume create --name myvolume

# 생성한 volume으로 container 실행 및 연결
$ docker run -i -t --name myvolume \
-v myvolume:/root/ \
ubuntu14:04

root@3kd1132jkhd:~# echo hello, volume! >> /root/test_volume
```
몰라도 되지만 volume 생성시 `/var/lib/docker/volumes/myvolume/_data` 여기에 volume 데이터 연동  
- `myvolume/_data` 데이터 X
  - container 데이터 존재하고 새로운 파일 write 하는 경우
  - container data(`/root/`) >> `myvolume/_data`으로 연동
- `myvolume/_data` 데이터 O
  - container(`/root/`) 디렉토리로 volume 데이터가 mount된다.(overwrite 됨)

<br>

## volume 삭제
- container 삭제
```bash
$ docker rm -f $(docker ps -a -q)
```
- volume 삭제
```bash
$ docker volume rm $(docker volume ls -q)
```
