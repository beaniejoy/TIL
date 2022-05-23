# git 다루기

- commit 수정하기 (amend)
- cherrypick
- reset
- revert
- stash
- remove file (remote repo)

<br>

## :pushpin: commit 수정하기

```bash
$ git commit --amend
```

- 최상위 커밋 내용을 수정하는 명령어
- 이전의 커밋을 없애고 새로운 커밋으로 변경하는 것(이전 커밋은 이력에서 없어짐)
- 누락된 커밋 내용이나 파일들이 있거나 오타 같은 내용에 대해 수정할 때 유용함  
  (굳이 새 커밋으로 하지 않고 기존 커밋을 수정하고 싶을 때)

```bash
$ git commit --amend

$ git push -f origin [branch]
```
- 원격 저장소에 push된 commit에 대해서 amend하고자 할 때 amend 기능 수행 후에 **force push**를 해야 한다.
- **개인 개발 브랜치에서만 수행할 것**

<br>

## :pushpin: cherrypick
- 특정 커밋 내용만 가져다 사용하고 싶을 때
- 기존 커밋 ID와 cherrypick으로 가져온 커밋 ID는 서로 다르다. (다른 커밋이라는 의미)

<br>

## :pushpin: reset
- 이전 커밋으로 브랜치를 되돌리고 싶을 때
- sourcetree에서 특정 브랜치의 과거 커밋으로 돌아가는 기능이 있다.  
  (Reset `[branch]` to this commit)
- mixed, soft, hard 모드 세 가지가 존재

### soft, mixed
- soft, mixed는 원하는 커밋으로 이력을 되돌리긴 하지만 중간에 이미 커밋된 이력들을 작업 공간으로 보여준다. (staged, unstaged)
- soft: staged 상태로 돌림
- mixed: unstaged 상태로 돌림'

### hard
- 과거 커밋으로 돌아갈 때 중간의 커밋된 내용들을 모두 없애고 깨끗한 상태로 과거로 돌아감
- 신중히 사용할 것


### add한 파일 되돌리기(Unstage로 돌리기)

```bash
$ git reset HEAD .
```

- `git add` 했던 내용들 중 다시 Unstage 영역으로 돌리고 싶은 파일이 있는 경우

<br>

## :pushpin: revert
- reset과 다르게 과거 커밋으로 돌릴 때 새로운 커밋을 만들어 되돌린 내용에 대한 이력을 또 남기는 것

<br>

## :pushpin: stash
- 임시 저장
- `untracked` 상태인 새로운 파일들은 stash 대상이 아니다.  
  (`tracked` 대상만 해당 - 한 번이라도 git push한 내용)

<br>

## :pushpin: Modified 파일 되돌리기

```bash
$ git checkout -- [File Name]
```

- staged 되기전 tracked 파일에 대해 modified가 이루어진 상태에서 위 명령어 수행
- **수행하면 수정된 내용은 전부 사라지고 원래 파일로 덮어씌어진다.** (많은 주의를 요구함)

<br>

## :pushpin: remote 올라간 파일 삭제하기

1. remote 파일 삭제
```baah
// 원격 저장소와 로컬 저장소에 있는 파일을 삭제한다.
$ git rm [File Name]
// 원격 저장소에 있는 파일을 삭제한다. 로컬 저장소에 있는 파일은 삭제하지 않는다.
$ git rm --cached [File Name]
```

2. `.gitignore` 설정  
원격에 삭제된 파일을 `.gitignore`에 추가

3. 변경내용 commit하기
```bash
// 버전 관리에서 완전히 제외하기 위해서는 반드시 commit 명령어를 수행해야 한다.
$ git commit -m "Fixed untracked files"
// 원격 저장소(origin)에 push
$ git push origin master
```