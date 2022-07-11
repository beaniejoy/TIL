# commit 수정하기

## :pushpin: 가장 최근의 commit 메시지 수정
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

## :pushpin: 가장 최근의 commit 자체 수정
- 기존 최근 commit에서 파일을 추가하거나 제외하고 싶은 것이 있고 기존 commit 내용을 교체하고 싶을 때 사용
- 추가할 (혹은 제외할) 파일 작업을 마치고 stage 상태로 올린다(add)
- `commit --amend`를 통해 기존 commit 내용을 수정하면 됨

<br>

## :pushpin: 여러 개 한꺼번에 commit 메시지 수정하기

```bash
$ git rebase -i HEAD~3
```
- `HEAD~3`를 기준으로 뒤에다가 재배치한다는 개념(그 뒤의 모든 커밋들을 새로 이어지게 만듬)

```bash
pick 3a58178 cart 기능 6 추가
pick 9754c59 patch 테스트
pick 3e90107 patch 2번째
```
- `pick`을 `edit`으로 바꾸게 되면 그 지점에서 stop하고 commit 메시지를 수정할 수 있다.

```bash
Stopped at 3a58178...  cart 기능 6 추가
You can amend the commit now, with

  git commit --amend

Once you are satisfied with your changes, run

  git rebase --continue
```
- edit 한 지점에서 위의 메시지가 나오는데 `git commit --amend`를 통해 메시지 수정
- commit 메시지 수정 완료되면 `git rebase --continue` 진행해서 다음 edit 지점으로 이동
- 이후 edit 지점이 없으면 rebase 작업이 끝나게 됨