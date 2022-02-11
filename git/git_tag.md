# tag

- tag는 커밋을 참조하기 쉽도록 커밋에 이름을 붙이는 것
- 특정 커밋에 한 번 붙인 tag는 고정된다. (이동 X)
- `Lightweight tag`, `Annotated tag` 두 종류 tag로 나뉨
- Lightweight
  - 태그에 이름만 붙임
- Annotated
  - 태그에 이름, 설명, 서명, 메타 정보들(만든 사람 이름, 이메일, 태그 생성 날짜) 포함
- 주로 Annotated tag를 사용 (아래 설명도 Annotated에 대한 내용)

<br>

## tag 만들기

```powershell
# 개발 작업을 통해 새로운 commit이 생성된 시점
$ git tag -a <tagname> -m "[tag에 대한 설명]"
# git tag -am "[tag에 대한 설명]" v0.1 < 이렇게 해도 되지만 위에껄 추천
```

- `-a`: annotated tag 생성하는 옵션
- `-m`: tag에 대한 설명 추가  
  (`-a`만 있는 경우 vi를 통해 설명을 작성하도록 되어있음)

<br>

## tag 확인하기

```powershell
$ git tag (-l, --list)
v0.1
v0.2
v0.3
v0.4
v0.5

$ git tag -n
v0.1            my version 0.1
v0.2            my version 0.2
v0.3            version 0.3
v0.4            version 0.4 : update main
v0.5            v0.5 updated
```

- 해당 repo에 존재하는 모든 tag 리스트업
- `-n`: tag에 대한 설명과 함께 모든 tag 리스트업

```powershell
$ git show <tagname>

$ git show v0.5
tag v0.5
Tagger: beaniejoy <lhb1025@gmail.com>
Date:   Fri Feb 11 19:11:35 2022 +0900

v0.5 updated

commit cfada2c077fe65fa35f1f5fef7786ffdda173809 (tag: v0.5)
Author: beaniejoy <lhb1025@gmail.com>
Date:   Fri Feb 11 19:11:26 2022 +0900

    main 0.5 updated

diff --git a/README.md b/README.md
index 9dda202..118aace 100644
--- a/README.md
+++ b/README.md
@@ -8,3 +8,4 @@ readme update origin

 main updated
 0.4 updated
+0.5 updated
```

- 특정 태그에 대한 정보 확인
  - 태그에 대한 설명
  - 태그 만든 사람정보(사용자이름, 이메일)
  - 태그 만든 날짜
  - 태그된 commit 정보 (커밋 포인터, 커밋 작성자, 커밋 날짜, 커밋 내용)
  - 커밋된 내용에서 바뀐 코드

<br>

## 리모트에 태그 업데이트

```powershell
$ git push origin <tagname> (or --tags)
```

- 태그를 만들어서 커밋에 붙이는 것만으로 리모트에 업데이트가 안됨(로컬에만 붙여짐)
- 리모트에 push를 해야 리모트에도 tag를 확인할 수 있다.
- 리모트에 업데이트할 tag 이름을 지정
- `--tags`를 통해 리모트에는 없고 로컬에만 있는 태그들을 모두 push 가능

<br>

## 추후에 태그 지정하기

```powershell
$ git log --pretty=oneline

98da0c90494e5c700e438a8de288dbb1a681bce3 (tag: v0.4) main updated
0b2324b8293971e828fcd7e24c3ff75a01c1d406 (tag: v0.3) main 긴급 수정
da5836f6d3a3e4f5b3d48f893a20767544c059e4 Update README.md
e4c6d6f9275f51787427bcad2635be8dfd13a9ed Merge branch 'iss53'
c58ebc23a035c7ffb3abdf3eae5dc7bbdd3933bb hotfix
** tag target > ccaaf15c4142f2e6be3da0670f2250c660136115 index.html commit
0f80e77ce9e041fb5847987a26c000a8cedcfdbc (tag: v0.2) v0.2 commit
58f029f52cc2339305a1666608ac46b37054b614 Merge pull request #1 from beaniejoy/version2
34493dfd80009f1c01d8b663cc1429779c31812a (origin/version2, version2) v0.1 updated
864eb246e298b38fd9fa1eccf82a2e0b74b2f994 tag test
```

- 이미 커밋된 내용에 태그를 추후에 붙이고 싶으면 먼저 log를 통해 태그 포인터를 알아야 한다.
- git log 명령을 통해 태그를 붙이고 싶은 커밋 주소를 복사한다. (앞 5자리까지만 복사)

```powershell
$ git tag -a v0.2.1 ccaaf

$ git tag -n
v0.1            my version 0.1
v0.2            my version 0.2
v0.2.1          new version v0.2.1  < 기존 커밋에 새로 태그 붙여짐
v0.3            version 0.3
v0.4            version 0.4 : update main

$ git push origin v0.2.1 (혹은 --tags)
```

- 복사했던 커밋 주소에 tag를 만든다.
- 만들고 태그 리스트업 해보면 새로 만들어진 태그를 확인 가능
- tag를 push해서 remote에 update 해준다.

## 특정 tag checkout

- 만약 리모트에 다른 사람이 만든 새로운 태그나 과거 특정 태그를 checkout할 수 있음

```powershell
$ git checkout <tagname>

$ git fetch
$ git checkout v0.5
Note: switching to 'v0.5'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

Turn off this advice by setting config variable advice.detachedHead to false

HEAD is now at cfada2c main 0.5 updated
```

- 리모트에 있는 태그를 가져오기 전에 fetch를 한번 하는 것이 좋다. (origin 내용 업데이트)
- git checkout 통해 원하는 태그에 checkout
- 태그를 checkout하면 `detached HEAD` 상태가 됨  
  커밋 주소에 checkout 된 상태, 브랜치가 없는 상태

```powershell
cfada2c0 v0.5 $ git add .
cfada2c0 v0.5 $ git commit -m "updated from v0.5"
54ef843 $
```

## tag 삭제

```powershell
git tag -d <tagname>
```
