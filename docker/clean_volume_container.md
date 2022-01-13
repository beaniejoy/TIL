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