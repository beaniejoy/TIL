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

## history

```shell
$ history
$ !!
$ !39
```
