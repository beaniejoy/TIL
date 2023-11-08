# Volume 관련

## volume overwrite

```shell
# volume 생성
$ docker volume create --name myvolume

# 생성한 volume으로 container 실행 및 연결
$ docker run -i -t --name myvolume \
-v myvolume:/root/ \
ubuntu14:04
```

## volume 삭제
- container 삭제
```bash
$ docker rm -f $(docker ps -a -q)
```
- volume 삭제
```bash
$ docker volume rm $(docker volume ls -q)
```

## Reference
- https://jojoldu.tistory.com/604