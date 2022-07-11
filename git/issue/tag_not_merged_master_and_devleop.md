
## description
- tag만 push된 상황이고 해당 tag에 develop master가 merge되지 않은 상황
- master, develop에 merge가 안됐기 때문에 다음 release가 merge되고 배포될 때 해당 누락된 tag는 전부 반영 안될 수 있음


## 해결과정
```bash
$ git checkout [머지되지 않은 태그]

(aaaaa) $ git checkout -b [임시 branch]

(임시 branch) $ git switch master
(master) $ git merge [임시 tag]
이 때 fast-forward merge이어야 함
(master) $ git push


(master) $ git switch develop
(develop) $ git merge --no-ff [머지되지 않은 태그]

(develop) $ git push
```