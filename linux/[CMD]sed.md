# sed command

- doc: https://www.gnu.org/software/sed/manual/sed.html

## file change(파일 내용 변경하기)
```
$ sed -i "s/^hello/world/g" test.txt
```

```
$ sed -i "s/\(hello\)/\1/g" test.txt
```
`\1`: 이전에 괄호안에 있는 내용을 그대로 지칭
```
$ sed -i "s/^\(hello\)\sworld/\1/g"
```
`\s`: whitespace
