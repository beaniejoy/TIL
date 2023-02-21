# SHELL & Command

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
$ history
$ !!
$ !39
```

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