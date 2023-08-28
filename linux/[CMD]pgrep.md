# pgrep command

## option

- `-c`  
실행중인 프로세스 개수 표시

```shell
$ pgrep -c java
1
```
mac os에서는 제대로 실행 안될 수 있음  

- `-f`
본래 검색 대상을 실행 중인 명령어 이름만이었다면 f option 넣으면 그 뒤에 인자들까지도 대상이 된다.  
java 같은 경우 뒤의 실행 명령어 option 기준으로 검색할 때 f option 사용 가능
```shell
$ pgrep Dspring.profiles.active=prod
$ pgrep -f Dspring.profiles.active=prod
123145
```

<br> 

## basename과 pgrep

```shell
$ ./test.sh
...sleep 상태

$ pgrep ./test.sh
아무것도 안나옴
```
shell script 실행시 pgrep으로 해당 script파일 실행프로세스를 찾으려고 하면 안나옴  
```
$ pgrep test.sh
123412
```
위와 같이 파일 자체 이름으로 pgrep 하면 프로세스 id가 나온다.  
```
$ pgrep `basename ./test.sh`
123412
```
그렇기 때문에 `basename`과 같이 사용해서 해당 스크립트 파일 실행 프로세스를 찾을 수 있을 것이다.
