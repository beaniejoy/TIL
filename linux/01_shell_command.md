# SHELL & Command

- Redirection
- history
- path
- shell 부팅 시퀀스
- shell script

<br>

## :pushpin: Redirection

### input
- `<`, `<<`
```shell
$ echo 'hello' > hello.txt
$ echo < hello.txt
```
- echo는 `<` input redirection 지원 X

```shell
$ cat < hello.txt
hello
```

```shell
$ cat
hello
hello
<ctrl+d>

$ cat << end
> hello
> world
> end
hello
world
```
- `cat`은 `ctrl + d`로 종료하기 전까지 입력받은 것을 그대로 출력해준다.
- `<<` delimiter 사용시 end 입력 때까지 계속 입력

### output

```shell
$ ls tmp/*
/tmp/VMwareDnD:
ls: cannot open directory '/tmp/snap-private-tmp': Permission denied
ls: cannot open directory '/tmp/systemd-private-398b9e9c52754e4dbda964de6d466a9c-ModemManager.service-7JHg5f': Permission denied
ls: cannot open directory '/tmp/systemd-private-398b9e9c52754e4dbda964de6d466a9c-colord.service-X5p2hj': Permission denied
ls: cannot open directory '/tmp/systemd-private-398b9e9c52754e4dbda964de6d466a9c-switcheroo-control.service-05zcyg': Permission denied
ls: cannot open directory '/tmp/systemd-private-398b9e9c52754e4dbda964de6d466a9c-systemd-logind.service-k5VOLf': Permission denied
ls: cannot open directory '/tmp/systemd-private-398b9e9c52754e4dbda964de6d466a9c-systemd-resolved.service-YwNxUi': Permission denied
ls: cannot open directory '/tmp/systemd-private-398b9e9c52754e4dbda964de6d466a9c-systemd-timesyncd.service-iwy7Xh': Permission denied
ls: cannot open directory '/tmp/systemd-private-398b9e9c52754e4dbda964de6d466a9c-upower.service-EOFWJi': Permission denied

/tmp/tracker-extract-files.1000:
ls: cannot open directory '/tmp/tracker-extract-files.125': Permission denied
```
위와 같이 permission denied 나옴 (standard error)  

```shell
$ ls /tmp/* > hello.txt
ls: cannot open directory '/tmp/snap-private-tmp': Permission denied
ls: cannot open directory '/tmp/systemd-private-398b9e9c52754e4dbda964de6d466a9c-ModemManager.service-7JHg5f': Permission denied
ls: cannot open directory '/tmp/systemd-private-398b9e9c52754e4dbda964de6d466a9c-colord.service-X5p2hj': Permission denied
ls: cannot open directory '/tmp/systemd-private-398b9e9c52754e4dbda964de6d466a9c-switcheroo-control.service-05zcyg': Permission denied
ls: cannot open directory '/tmp/systemd-private-398b9e9c52754e4dbda964de6d466a9c-systemd-logind.service-k5VOLf': Permission denied
ls: cannot open directory '/tmp/systemd-private-398b9e9c52754e4dbda964de6d466a9c-systemd-resolved.service-YwNxUi': Permission denied
ls: cannot open directory '/tmp/systemd-private-398b9e9c52754e4dbda964de6d466a9c-systemd-timesyncd.service-iwy7Xh': Permission denied
ls: cannot open directory '/tmp/systemd-private-398b9e9c52754e4dbda964de6d466a9c-upower.service-EOFWJi': Permission denied
ls: cannot open directory '/tmp/tracker-extract-files.125': Permission denied
```
오류에 대해서는 stdout에 출력하고 성공에 대해서는 `hello.txt`에 담는다.

```shell
$ ls /tmp/* > hello.txt 2> error.txt
```
성공한 내용은 `hello.txt` 에러는 `error.txt`

<br>

## :pushpin: history

```shell
$ history [size]
$ !!
$ !39
$ history -c
```
`history 10`: 최근 10개를 보여준다.
`-c`: history 버퍼 삭제(clear)  
`!!`: 바로 이전 명령어 실행
`!15`: 15번째 라인 명령어 실행

- history size
```shell
$ echo $HISTSIZE
1000

$ echo $HISTFILESIZE
2000
```
`$HISTSIZE`: 최근 몇 개를 보여줄 것인가(버퍼링 개수, 기본 1000)  
`$HISTFILESIZE`: 파일에 기록할 수 있는 size(쉘 종료시)

<br>

## :pushpin: PATH

```shell
$ echo $PATH
```
명령어 실행할 때 해당 프로그램 찾는 순서
1. PATH 디렉토리 확인
2. 실행권한 확인
   1. `SetUID` 확인
3. 명령어를 해당 사용자 ID로 실행
   1. 해당 명령어의 소유주 권한으로 명령어 실행

```shell
$ which [FILENAME]
```
실행하는 명령어의 위치
```shell
$ printenv
```
모든 설정된 환경변수 확인 가능

<br>

## :pushpin: Prompt

```
$ echo $PS1
```
명령 프롬프트(prompt) 변경

<br>

## :pushpin: shell 부팅 시퀀스

BASH's  interactive shell 시작 시퀀스
- `/etc/profile` (공통 수행 - 환경 설정들)
  - `/etc/profile/d/*.sh`
  - `/etc/bash.bashrc`
- `~/.profile` (사용자별 디렉토리 - 시작프로그램 등)
  - `~/.bashrc`
    - `~/.bash_aliases` (있다면 추가적으로 수행)

<br>

## :pushpin: shell script

```shell
#!/bin/bash

while read line
do
        echo $line
done < $1
```
```shell
$ ./test.sh hello.txt
hello
world
```
line으로 한 줄씩 읽어서 출력(echo)  
`$1`: 실행할 때 입력한 첫 번째 인자(parameter) - 여기서는 `hello.txt`  
`$@`: 모든 인자(argv), `$1 .. $n`  

```shell
#!/bin/bash

echo "target file: $1"

while IFS=: read -r F1 F2 F3 F4 F5 F6 F7
do
        echo "사용자 $F1 는 $F7 쉘을 사용하고 $F6 홈디렉토리를 사용"
done < $1
```
```shell
$ ./test2.sh /etc/passwd
```
`IFS`: Internal Field Seperator(구분자 같은 역할)  
기본값: `<space><tab><newline>`

- 입출력 인자값
  - `$0`: 스크립트/명령어 이름
  - `$#`: 전달된 파라미터 개수
  - `$$`: 프로세스 번호
  - `$?`: 실행결과
  - `$1, $2, ...`: 입력 인자
  - `$*`: 입력 인자 모두

<br>

## :pushpin: References

- [Linux - CentOS 리눅스 주요 특수 기호(메타문자)](https://hack-cracker.tistory.com/26)