# checkout

<br>

## origin에만 있는 branch 가져오기
- local에는 없고 origin에만 있는 branch 가져올 때 checkout 이용

```bash
$ git checkout -b develop --track origin/develop
```