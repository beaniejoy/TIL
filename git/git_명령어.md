# git 명령어 모음

<br>

## 상태 확인

### diff

```powershell
$ git diff --cached
```

- `--cached`, `--staged`: 같은 옵션, staged 상태의 파일들 차이점 비교

### log

```powershell
$ git log
```

- `-p`, `--patch`: 각 커밋의 diff 결과를 보여준다.

<br>

## Remote Repo

```bash
$ git remote
origin
$ git remote -v
origin	https://github.com/beaniejoy/exercise-git.git (fetch)
origin	https://github.com/beaniejoy/exercise-git.git (push)
```

- remote 설정 확인하기(url)

```bash
$ git remote add beanie [repo:url]
```

- remote 저장소 추가

```bash
$ git remote show origin
```

- 리모트 저장소의 구체적인 정보 확인
- `git pull` 명령을 실행할 main 브랜치, merge할 브랜치 list up

```bash
$ git remote rename beanie joy
$ git remote remove joy
```

- `rename`: 리모트 이름 변경
- `remove`: 리모트 삭제

<br>

## merge

```bash
$ git checkout -b feature/issue
...
$ git add .
$ git commit -m "feature/issue now developing"

$ git checkout main
$ git checkout -b hotfix
...
$ git add .
$ git commit -m "hotfix commit"
$ git checkout main

$ git merge hotfix
Updating 0f80e77..c58ebc2
Fast-forward
 index.html | 3 +++
 1 file changed, 3 insertions(+)
 create mode 100644 index.html

$ git branch -d hotfix
Deleted branch hotfix

$ git checkout feature/issue
...
$ git add .
$ git commit -m "feature/issue completed"
$ git checkout main

$ git merge feature/issue
CONFLICT (add/add): Merge conflict in index.html
Auto-merging index.html
Automatic merge failed; fix conflicts and then commit the result.

(after solving the conflict)
$ git add index.html
$ git commit
$ git push

$ git branch -d feature/issue
Deleted branch feature/issue
```

- merge 하는 과정
- conflict 발생시 해당 파일을 통합 수정해서 add
- conflict 해결 후 commit 하면 merge에 대한 내용 기입 후 push하면 된다.
