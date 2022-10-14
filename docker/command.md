# Docker 관련 명령어 정리

```bash
$ docker create [Image_Name]
```
- 이미지에 있는 파일 스냅샷을 컨테이너에 할당받은 하드디스크에 올림
- 컨테이너를 생성하는 과정


```bash
$ docker start [Image_Name/Container_ID]
```
- 이미지에서 시작시 실행될 명령어를 컨테이너로 옮기고
- 해당 컨테이너를 실행
- `-a`: attachment, docker container가 실행될 때 붙어있다가 실행결과를 화면에 보여줌


```bash
$ docker run [Image_Name]

$ docker run [Image_Name] [명령어]
```
- `run`: `create`, `start`를 동시에 진행해준다.
- `-d`: detach, 실행해서 컨테이너에 붙었다 바로 떨어짐. (다시 command로 돌아옴)
- `-p`: port 설정, local과 container 간에 network port 연결
- `[명령어]`: 원래 이미지가 가직고 있는 시작 명령어 무시하고 해당 커맨드 실행(ex. `ls`, `ping localhost`)

```bash
$ docker exec [Container_ID] [명렁어]
```
- `exec` vs `run`
- `-it`

```bash
$ docker stop [Container_ID]

$ docker kill [Container_ID]
```
- `stop`
- `kill`

```bash
$ docker ps

$ docker ps -a

$ docker ps --format '{{.Names}} \t {{.Image}}'
```
- `ps`: 현재 실행되고 있는 컨테이너 내용 표시
- `ps -a`: 모든 컨테이너 목록 표시
- `ps --format 'table \t table'`: 원하는 항목만 컨테이너 정보 표시

```bash
$ docker build ./
```
- `build`: Dockerfile을 기준으로 build를 해서 Image 생성
- `-t`: tag설정, image 이름을 붙여준다.

```bash
$ docker images
```

```bash
$ docker rm [Container_ID]

$ docker rmi [Image_ID]
```

