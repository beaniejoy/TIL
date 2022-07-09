# HEAD 관련 내용

- HEAD
- ORIG_HEAD
- FETCH_HEAD(알아 볼 것)

<br>

## :pushpin: HEAD
- git은 커밋주소가 있고 이를 가리키는 포인터 개념이 존재
- 현재 어떤 커밋에 위치하는지 가리키고 있는 포인터가 `HEAD`
  - 쉽게 말해서 **현재 커밋 위치**

예시)
```bash
$ git show HEAD
commit e54995a17ba11ba85e4e5d62fc016ec1ef4555e1 (HEAD -> master, tag: vGIT-TEST, origin/master, origin/HEAD)
Merge: b4d51de 8750b77
Author: joybeanie <joybeanie@gmail.com>
Date:   Sat Jul 9 01:35:33 2022 +0900

    Merge branch 'release/vGIT-TEST'

```

<br>

## :pushpin: ORIG_HAED
- 현재 HEAD 바로 직전의 커밋 위치를 가리키고 있는 `HEAD`를 `ORIG_HEAD`
- 사용 사례
  - merge 할 때 그 이전 상태로 돌아갈 때 사용  
  (fast-forward, merge conflict 관계없이 이전 상태로 돌아감)
```bash
$ git reset --hard ORIG_HEAD
```
- 현재 HEAD 바로 이전 커밋을 가리키기 때문에 신중하게 사용해야 한다.
