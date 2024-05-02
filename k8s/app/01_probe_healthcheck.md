# Probe & Health Check

## Probe 설정

- **initialDelaySeconds**
container가 뜨고 첫 probe 요청하기까지 시간 설정
애플리케이션 실행되고 잘 뜰 때까지의 시간을 고려해야함(너무 길지도 않게)

- **periodSeconds**
  probe 사이의 주기를 설정, probe 성공(실패) 이후 다음 probe 요청까지의 시간
  이것도 적절하게 설정하는 것이 좋다. 짧으면 애플리케이션 상태를 민감하게 관찰 가능
  보통 1초로 잡기는 한다.

- **timeoutSeconds**
  probe 요청 보냈을 때 어느정도 시간까지 응답이 없으면 실패로 처리할 지 설정
  대부분의 실패케이스가 timeout 케이스
  이 설정은 너무 짧게, 너무 길게 잡지 않도록 설정하는 것이 중요
  1초 정도 잡는 것도 무리가 가지는 않는다. (애초에 api 응답속도가 1초가 넘는다는게 비정상일 수 있음)

- **successTreshold**, **failureTreshold**
  몇 번 연속으로 probe가 성공 혹은 실패인 경우 해당 상태를 컨테이너의 상태로 지정해줄 것인지 결정

- **terminationGracePeriodSeconds**
  probe 실패인 경우 컨테이너 재시작해야하는 경우 어느정도 유예시간을 두고 컨테이너를 재시작할지 시간 설정
  Pod에서도 설정가능(scaling 하는 경우도 포함), probe에 설정하는 경우 probe 설정이 우선시된다.
  (나중 버전에 포함)

<br>

## Probe 종류

- **startup**
  다른 probe를 실행할지 말지 결정하는 probe (나중 버전에 포함)
  initialDelaySeconds는 정적인 시간으로 설정하는 것, failureTreshold * period 값이 애플리케이션 실행 완료 시간을 포괄해야 함
- **readiness**
  해당 pod를 endpointslice에 포함할지 말지를 결정
  해당 probe를 통과해야지 endpointslice에 포함된다.
  컨테이너가 생성되었을 때 해당 컨테이너가 실제로 요청을 처리할 준비가 되었는지 판단
  successTreshold를 설정 가능, 몇 번 연속 성공해야 네트워크에 연결해줄지 설정 가능
  만약 해당 probe가 실패인경우 endpointslice에 제외되고 다시 성공으로 돌아오면 포함됨
- **liveness**
  컨테이너의 상태가 정상인지 아닌지 판단하는 probe
  설정 시간이 오버되어버려 실패하는 경우 **컨테이너를 재시작**하게 된다. (파드가 아니라 컨테이너가 재시작)
  재시작 후에 다시 probe 동작
  다시 시작하는 작업이 동반되기 때문에 다른 probe에 비해 오버헤드가 크다
  해당 probe 설정을 잘해야 한다.
  
ReadinessProbe는 LivenessProbe에 비해 더 민감하게 설정해야 한다.
컨테이너 재시작이 서비스에 어떤 영향을 주는지 고려해서 설정해야 한다.
Probe 설정에 따라 배포에 걸리는 시간도 달라진다.
probe 체크는 컨테이너 내부의 프로세스에 한정해야 하며 부담이 가는 동작이 아니어야 한다.

warm up 같은 경우 spirng application 기능에서 별도로 설정하는 것이 좋다. (probe에서 하는 것은 권장 X)
probe는 짧은 주기로 하되 treshold 값을 통해 민감도를 조절하는 것이 좋아 보임

<br>

## Graceful Shutdown

애플리케이션의 종료 시 작업을 정리할 유예 시간을 주는 절차

SIGTERM (먼저 신호를 보내주고) > SIGKILL (종료)
이 사이의 시간 설정을 `terminationGracePeriodSeconds`로 설정

```yaml
# pod spec
spec:
  terminationGracePeriodSeconds: 60
  containers:
    #...
```
LivenessProbe 실패 외의 모든 종료 동작은 pod spec에 정의한 설정대로 동작하게 된다.
spring application 자체에도 설정가능

<br>

## Pod Lifecycle Hook

```
Created >> `postStart` >> Running >> `preStop` >> Terminated
```
- postStart: 컨테이너 시작한 직후 실행되는 hook
  - container 생성되자마자 postStart 시작(애플리케이션 실제 실행시점과 무관)
  - 일반적으로 많이 사용되는 hook은 아니다.
- preStop: 컨테이너 종료될 때 실행되는 hook
  - **terminationGracePeriod: 30s 일 때 > preStop 10s / SIGTERM - SIGKILL 20s** 이렇게 분배

`preStop` Hook에서 sleep 명령 등으로 약간의 텀을 주는 방식도 일반적인 구현으로 사용가능  
SIGTERM 신호 전달하기 전에 어느정도 애플리케이션 요청 처리를 정리한 다음에 여유 있는 상황에서 신호 전달하도록 유도
**graceful shutdown에만 의존하는 것이 아니라 미리 어느정도 트래픽을 정리하는 것이 좋음**
(애프리케이션 단계에서 graceful shutdown을 완벽하게 수행하지 못하는 경우도 존재하기 때문)

