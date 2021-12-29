# NVM으로 Node.js 버전 관리하기

```bash
$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash

$ source ~/.zshrc
$ command -v nvm
nvm <- 나와야 정상
```
- 위와 같이하면 nvm 설치 완료

```bash
$ nvm install --lts
```
- lts 최신 버전 설치
- 특정 버전 설치시 `v` prefix 붙이고 버전 번호 넣어서 입력하면 됨

```bash
$ nvm ls
```
- 설치된 node.js 버전 확인