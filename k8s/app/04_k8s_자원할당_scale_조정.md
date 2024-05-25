# 자원할당 & scale 조정

- Vertical Scaling: workload 자체 컴퓨터 자원 조정
- Horizontal Scaling: workload 수량 조정

두 측면에서의 scaling 모두를 고려해야 함

```yaml
resources:
  requests:
    memory: "512Mi"
    cpu: "250m"
  limits:
    memory: "1Gi"
    cpu: "500m"
```
- `requests`: Pod가 노드에 배치될 때 확보되어야 하는 자원
- `limits`: Pod가 최대한 사용할 수 있는 자원

requests를 적당하게 설정해야 함, 너무 높게 잡아버리면 노드에 파드가 스케줄링 되지 못하는 상황 발생 가능

**requests, limits 항상 설정해주는 것이 좋다.**

- memory: 메모리 양 설정
  - Mi, Gi > 일반적인 메가바이트, 기가바이트하고 용량 차이가 있지만 무시해도 될 정도
- cpu: CPU Time을 코어로 환산
  - 1, 2 코어 개수로 설정 가능
  - 250m, 500m > 0.25 코어, 0.5 코어 (1m: milli core, 1/1000 코어를 의미)

<br>

## JVM Memory 설정

JVM 버전에 따라 Heap 메모리 할당이 달라짐
별도의 JVM 옵션으로 Heap 메모리를 할당

- `-Xms`, `-Xmx`
  - 고정 heap 사이즈 설정(최소, 최대치)
- `-XX:InitialRamPercentage`, `-XX:MaxRamPercentage`
  - 메모리에 대한 비율로 설정(java 10 버전부터 지원)
  - 컨테이너 환경에서 동적으로 할당 가능
  - MinRamPercentage도 줄 수 있지만 Initial 설정되어있다면 굳이 필요 X
  (`InitialRamPercentage`: 컨테이너에 처음 할당할 Heap 메모리 비율)

- `XX:+ExitOnOutOfMemoryError` 
  - JVM 상에서 OOM이 발생해도 쿠베에서는 아무런 조치를 해주지 않는다. 
  (JVM 상에서 OOM이어도 컨테이너 전체체서는 아닐 수 있기 때문)
  - 해당 옵션을 주면 JVM OOM 발생시 자바 프로세스를 명시적으로 종료 > 쿠베에서 컨테이너 재시작

<br>

## CPU Resource 설정

실제 사용하는 코어 개수가 아니라 노드의 CPU를 점유하는 비율을 뜻하는 것임
2000m / 8 core node >> 실제 8개 코어 중 2개 코어를 사용하겠다 그것이 아님 
전체 코어에서 2000m 비율 만큼 CPU Time을 할당하겠다 그런 의미임
CPU 자원이 부족할 경우 Throttling 발생 가능

Spring MVC 같은 요청 당 스레드 구성인 경우 CPU 자원을 조금 높게 잡는 것이 좋다.

<br>

## Deployment replica 조정

스펙의 replicas 설정을 통해 파드 개수 조절 가능
명령어 통해서도 변경 가능
```shell
kubectl scale deployment my-app --replicas=5
```
명령 통해서 변경했다면 이후 배포에도 유지하기 위해 스펙 스크립트 파일에도 적용 필요

> 파드 개수를 늘리는 것이 성능을 선형적으로 올려주지는 않는다.
> DB, 외부 병목 지점 발생 가능성도 따져봐야 한다.
> 노드의 자원은 어느 정도 여유를 가지고 사용하는 것이 중요

Spring MVC 같은 경우 스레드 200개씩 사용한다 쳤을 때 cpu 자원이 확보가 되어야한다.
Spring Webflux 같은 비동기 서버같은 경우 적은 코어수로 여러 개 파드 운영하는 것이 효율적일 수 있다.