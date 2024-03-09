# kube object - workload

## Pod

쿠베에서는 컨테이너를 바로 실행할 수 없음 > 파드라는 개념을 사용

> Pod: kube가 사용하는 가장 기본적인 배포 단위

pod보다 작은 단위로 배포하는 것은 불가능하다는 의미, 하나의 pod 안에 여러 컨테이너들이 존재할 수 있음  
같은 파드의 컨테이너는 같은 환경을 가짐  
(쿠베에서는 파드별로 격리된 환경을 가진다고 보면 되고 그 안에 컨테이너들은 안에서 동작하는 프로세스라고 생각하면 된다.)
**파드 안의 컨테이너들은 서로 다른 포트를 사용해야한다.**

처음에는 파드와 컨테이너를 동일시 생각하면 된다.

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-simple-pod # 이 객체를 식별하는 일종의 id 역할
spec:
    containers:
    - name: my-container
      image: nginx:1.24
```
`metadata`에는 label, name과 같은 것들이 담기게 되는데, 사용자가 직접 선언적으로 명시하는 내용이 있고, 쿠베에서 자동으로 생성되는 내용이 있다.

```shell
kubectl apply -f my-simple-pod.yaml
```
위의 yaml 스펙을 쿠베에 apply로 적용할 수 있다.

<br>

## Pod Phase

파드의 라이프사이클

```
Pending, Running, Failed, Succeeded, Unknown
```
- Pending: 파드 생성한 직후 노드에 배치되기 전까지의 단계
  - 스펙 적용, 파드 생성, 이미지 다운로드 완료 시점까지
- Running: 파드가 실행 중인 단계
  - 실행에서 종료되기 전까지 (대부분 이 상태)
- Failed: 컨테이너 비정상 종료
  - exit code가 0이 아닌 상태(잘 나타나지 않는다. > 쿠베 재시작 정책 때문)
  - Restart Policy(파드 안의 컨테이너 재시작 정책)
    - Always(항상 종료된 컨테이너 재시작, default), OnFailure(실패한 컨테이너만), Never(X)
- Succeeded: 컨테이너가 정상적으로 종료된 상태
  - 재시작 정책이 always면 해당 phase는 볼 수 없다.
- Unknown: 파드의 상태를 알 수 없는 경우
  - 보통은 파드 단위로 발생하지 않고 노드단위로 발생할 수 있다.

여러 컨테이너를 가지고 있는 경우 상태 결정

- 하나 이상의 컨테이너가 실행 중인 상태면 Pod Running으로 간주
- 모든 컨테이너가 종료된 상태에서 실패한 컨테이너가 하나라도 있는 경우 Pod Failed 간주
- 모든 컨테이너가 종료 및 정상 종료돈 경우 Succeeded

<br>

## Container Status

컨테이너도 상태를 가짐

- Waiting
- Running
- Terminated

container restart with backoff
- 컨테이너 재시작 할 때 주기적으로 간격을 늘려가며 재시작한다.
- `1st` - 10s - `2nd` - 20s - `3rd` - 40s - `4th` - 80s - `5th`
- 재시작하는 것 자체에 throttling을 걸고 컨테이너가 계속 죽어버리는 상황이 해결되기를 기다린다고 볼 수 있다.
- 최대 5분까지 늘어남
- 재시작하다가 정상적으로 컨테이너가 뜨게 되면 재시작 시간 초기화 된다.

<br>

## Probe

파드의 상태를 모니터링 하기 위한 경로  
단순히 프로세스가 동작하고 있다고 컨테이너가 정상동작한다고 판단할 수 없다.

- readiness: 파드를 네트워크에 연결할 지 결정
  - exit code 0번 혹은 Http Code 200 ok >> 설정에 따라 성공판단이 달라짐
- liveness: 파드 재시작 할지 결정
  - 컨테이너가 정상 상태인지 판단하는 probe, 주기적으로 컨테이너에 보내다가 정상 응답이 오지 않으면 재시작
  - 프로세스는 살아있지만 제기능을 하지 못하는 상태인 경우 알아서 재시작 시켜줌
- startup: readiness, liveness probe를 보낼지 말지를 결정
  - 먼저 startup 체크를 하고 그다음 readiness, liveness 체크하게 됨
  - 애플리케이션이 처음 정상적인 상태에 진입하기까지 startup을 통해 기다려줌, 텀을 준다는 개념

<br>

## Graceful Shutdown**

파드 종료할 때 주는 유예기간, 기존 처리하고 있던 요청을 정상적으로 처리되고 종료할 수 있도록 해준다.

- SIGTERM: application graceful shutdown 유도
- SIGKILL: 설정된 시간을 초과하는 경우 강제 종료

<br>

## Container Lifecycle Hook

컨테이너 시작, 종료 시점에 실행되는 명령어  
- `postStart`: 컨테이너 시작 직전
- `preStop`: 컨테이너 종료 직전