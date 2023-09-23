# Shell Script 작성 팁

shell script 작성하면서 알게된 팁 기록용

<br>

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

<br>

## Regular Expression(정규 표현식)

```shell
[[ $line =~ [[:space:]]*(a)?b ]]

status_code=200
if [[ $status_code =~ ^2[0-9]{2} ]]
```
shell script에서 정규표현식 비교를 하고 싶다면 `=~` operator를 이용하는 것이 좋다.
- https://www.gnu.org/software/bash/manual/html_node/Conditional-Constructs.html#index-_005b_005b

<br>

## 조건문

### -z (null, empty string check)
```shell
my_var="anything"
if [ -z "$my_var" ]
then
      echo "\$my_var is NULL"
else
      echo "\$my_var is NOT NULL"
fi
```
**위의 조건식이 true가 되는 경우**
- 변수가 선언이 안되어있는 경우
- 변수가 선언되었는데 값 할당이 안되어있는경우 (`my_var=`)
- 비어있는 문자열인 경우 (`my_var=""`)

```shell
if [ -z "$my_var" ]
if [[ -z "$my_var" ]]
if test -z "$my_var" 
```

### -n (check nonzero length string)
```shell
#!/bin/bash
var=""
if [ ! -n "$var" ]
then
	echo "null or zero length string"
else
	echo "nonzero length string"
fi
```
빈 문자열이 아닌 변수에 대해 true 반환  
변수에 대해 빈 문자열이거나 선언 혹은 할당되지 않은 경우 false 반환  
`! -n` > 이거 대신 `-z` 사용하자(`-n`, `-z`는 서로 반대)