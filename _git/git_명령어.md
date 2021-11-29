# git 명령어 모음

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

## 되돌리기

### commit 수정하기
```bash
$ git commit --amend
```
- 이전 커밋 내용을 수정하는 명령어
- 이전의 커밋을 없애고 새로운 커밋으로 변경하는 것(이전 커밋은 이력에서 없어짐)
- 누락된 커밋 내용이나 파일들이 있거나 오타 같은 내용에 대해 수정할 때 유용함  
  (굳이 새 커밋으로 하지 않고 기존 커밋을 수정하고 싶을 때)

### add한 파일 되돌리기(Unstage로 돌리기)
```bash
$ git reset HEAD .
```
- `git add` 했던 내용들 중 다시 Unstage 영역으로 돌리고 싶은 파일이 있는 경우

### Modified 파일 되돌리기
```bash
$ git checkout -- [File Name]
```
- staged 되기전 tracked 파일에 대해 modified가 이루어진 상태에서 위 명령어 수행
- **수행하면 수정된 내용은 전부 사라지고 원래 파일로 덮어씌어진다.** (많은 주의를 요구함)

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

## tag

- tag와 기존 브랜치와 별개로 움직임
```bash
$ git commit -a -m "v0.1 commit"

$ git tag -a v0.1 -m "my version 0.1"

$ git push origin v0.1
```