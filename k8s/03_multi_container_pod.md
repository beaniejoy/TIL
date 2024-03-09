# Multi Container Pod

쿠베의 파드는 애플리케이션 단위와 비슷

파드 안에 하나의 애플리케이션 프로세스일 수도 있고 여러 개의 프로세스를 가질 수 있다.  

하나의 컨테이너는 하나의 프로세스를 가져야 한다.(꼭 그런 것은 아니지만 하나의 프로세스만을 상정하는 것이 좋다.)  
여러 개 프로세스를 가지는 경우 컨테이너 상태를 다루기가 까다로워짐  

파드가 여러 컨테이너를 포함하는 구성을 사용할 수 있게 함(컨테이너 - 프로세스 1대1 매핑으로 인해)

파드는 특정 노드에 매핑되엉 있음

<br>

## Restart Policy

- Always: 모든 컨테이너가 상시 실행되어야 할 때
  - nginx, application 두 개 모두 정상 상태여야 제대로 동작하는 상황일 때
- OnFailure: 어떤 컨테이너는 작업 후 종료가 가능할 때
  - 정상 종료에 대해서는 재시작 수행 X, 실패난 컨테이너가 존재하는 경우 파드 재시작
- Never: 파드 종료 상태를 보고 별도로 처리할 때

<br>

## Init Container

파드의 초기화를 위해 사용하는 컨테이너  
실제 실행하고자 하는 컨테이너들을 실행하기 전에 먼저 떠서 특정 작업을 수행한 후에 없어지는 역할 수행

```yaml
spec:
  initContainers:
  - name: my-initializer
  - image: app-initializer
```
init container도 실제 실행될 main containers과 같은 파드(노드)에서 실행되기 때문에 같은 리소스를 참조하게 된다.

shell script, wget, curl 같이 애플리케이션 초기화할 때만 사용되는 것들이 있다면 init container에서 수행하게 할 수 있다.  
(공유되는 저장공간에 외부에서 받아온 라이브러리 같은 것들을 init에서 미리 작업)

initContainer는 정상적으로 종료되어야 다음 실제 container들이 실행될 수 있음

여러 개의 컨테이너를 init container로 지정 가능  
yaml에 지정한 순서대로 (위에서 아래로) 순차 수행

containers에 지정된 main 컨테이너들은 실행 순서가 정해진 것 없이 동시에 실행됨(순서가 보장되지 않음, docker-compose와 비슷)

