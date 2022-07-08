# merge 관련 필기


## fast foward
- `--ff`: 기본 옵션, fast-foward에 대해서 ff로 바로 처리
- `--no-ff`: fast-forward인 상황에도 반드시 병합 커밋 생성

```bash
(master) $ git merge topic
```
![232FEE4F54F3155E34](https://user-images.githubusercontent.com/41675375/178031321-2884a74e-5649-4f61-ba14-405d5122468b.png)

```bash
(master) $ git merge --no-ff topic
```
![2641324F54F3155F1F](https://user-images.githubusercontent.com/41675375/178031336-36e59325-081f-4ea8-9cb2-dfdb0caa4bdd.png)
- 이 때 반드시 병합 커밋 생성됨(`Merge branch 'topic'`)

<br>

## squash
```bash
(master) $ git merge --squash topic
```
![img1 daumcdn](https://user-images.githubusercontent.com/41675375/178034734-c8351410-1c8e-4f4a-a458-5bd4733bec3f.jpg)
- squash flag는 합치고자 하는 대상 브랜치의 모든 커밋들(공통분모로부터)을 모아서 합치는 브랜치(master)에 새로운 커밋 생성
- **대상 브랜치의 최신 커밋과 연결 짓지 않음**