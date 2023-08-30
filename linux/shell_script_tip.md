# Shell Script 작성 팁

shell script 작성하면서 알게된 팁 기록용

## 중복 실행 방지

```shell
#!/bin/bash

if [ $(pgrep -c `basename $0`) -gt 1 ]; then
	echo -e "don't try multiple running"
	exit 10
fi

...
```
application 실행 스크립트면 해당 스크립트 파일에 대해서 중복 실행은 방지해야 할 것이다.  
이 때 사용되는 것이 `pgrep`, `basename` 조합이다.  
```shell
$ ./test.sh

$ pgrep ./test.sh
> 아무것도 검색이 안됨
$ ps -efL | grep ./test.sh
ec2-user 12345 13235  0 21:19 pts/0    00:00:00 /bin/bash ./test.sh
```
위의 내용 처럼 test.sh 실행했을 때 프로세스 검색을 `pgrep ./test.sh`로 하면 검색이 안된다.  
(실제로 프로세스는 `./test.sh` 이름으로 돌아가는 중)  
`pgrep test.sh` 에 대해서 검색이 되기 때문에 basename으로 실행된 스크립트 이름만 나오도록 해야함

