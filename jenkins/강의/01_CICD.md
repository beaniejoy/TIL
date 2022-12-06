# Jenkins 활용한 CI/CD 구축

## :pushpin: 새로운 Item 생성

- 프로젝트 생성
  - Freestyle project
    - 여기서 `Build Step` > `Execute Shell` 테스트 수행
  - Maven project
    - Maven 기반의 젠킨스 프로젝트 생성
    - maven clean > compile > package 과정을 통해 war 파일 패키징
    - git repository 설정도 가능

<br>

## :pushpin: Poll SCM
- Build periodically > cron job
  - 코드 변경사항이 없어도 일단 빌드를 다시 수행
- Poll SCM > cron job
  - github에서 데이터를 가져올 때 신규 commit이 있는 경우에만 빌드 다시 수행
- 젠킨스 설정에서는 빌드 유발(Build trigger)에서 설정 가능

<br>

## :pushpin: Publish Over SSH
- 젠킨스에서 페키징 완료한 파일을 다른 Server로 전송할 수 있는데 SSH 관련 플러그인 설정을 해야 한다.
- Jenkins 관리에서 Plugins에서 다운로드
- 그다음 환경설정(`Configure System`) > `Publish over SSH`에서 SSH Server 내용 설정
  - name
  - hostname: [Remote IP] 같은 로컬에 있는 Server를 설정하고자 할 때 private ip로 입력해야 한다.
  - username: root(해당 서버의 사용자)
  - password
  - port: 22(실습에서는 10022로)
- `Test Configuration`으로 연결확인(SSH Server가 기동이 되어 있어야 한다.)

<br>

## :pushpin: 프로젝트에 SSH 설정하기
- 프로젝트 설정에서 빌드 후 조치
- `Send build artifacts over SSH`
- SSH Server 설정(전체 환경설정에서 `Publish over ssh`에 설정해두었던 SSH Server 내용 적용)
- `Source files`
  - 젠킨스 서버에 있는 파일 중 원격지로 전송할 대상 파일 지정
- `Remove prefix`
  - source files에서 설정해둔 대상 파일 경로에서 prefix로 붙여진 경로를 삭제하고 파일만 전송하고 싶을 때 사용 가능
  - `target/*.war` > `*.war` (prefix: `target`)
- `Exec command`
  - 파일 전송된 이후 원격지 서버에서 실행되는 명령어 설정



