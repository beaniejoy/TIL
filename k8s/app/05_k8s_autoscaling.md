# k8s autoscaling

Pod의 현재 자원 사용량을 모니터링 해서 적정한 자원을 자동으로 설정해주는 방식

- HPA: Horizontal Pod Autoscaler (k8s 기본 기능)
- VPA: Vertical Pod Autoscaler (베타 단계, 오픈소스 소프트웨어, 벤더사에서 제공)

```
Monitor > Analyze > Plan > Execute > Monitor
```
위와 같은 사이클을 돌면서 지속적으로 파드 상태 확인

<br>

## HorizontalPodAutoscaler

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 1 # HPA
  maxReplicas: 5 # HPA
  metrics:
    - type: Resource # 목표
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50 # HPA (평균적인 cpu 점유율이 50퍼 가깝게 유지되도록)
```
- `scaleTargetRef`: 스케일 타겟은 스케일이 가능한 객체여야 한다.
  - replicas 속성을 가진 객체만 가능하다고 보면 된다.
  - Deployment, ReplicaSet, StatefulSet, 대부분 Deployment를 지정
  - StatefulSet에 지정하려면 PV를 미리 지정해주거나, dynamic provisioning 고려해야함
- `minReplicas`: 평소 사용하는 수량
  - 그렇다고 너무 최소한으로 설정하면 급격하게 늘어나는 트래픽에 대응이 잘 안될 수 있다.
  - 파드 띄우는 시간도 소요되기에
- `maxReplicas`: 노드에 무리가 안되는 선에서 
- `metrics`: autoscaling에서 자원의 기준 값
  - cpu, memory가 일반적임
  - Ingress 유입되는 트래픽 양을 기준으로 해서 커스텀하게 설정가능

```
적정 replica 수량 = ceil(현재 replica 수량 * (현재 Pod Utilization 평균치 / Target))
```
HPA는 아주 즉각적으로 발생하지 않는다는 것을 기억해야 함

<br>

## VerticalPodAutoscaler

Pod의 자원 사용량에 따라 cpu, memory 할당량을 조정해주는 autoscaler
적절한 자원 할당량 찾기 위해 사용 가능
주의할 점은 컨테이너 재시작을 유발할 수 있다는 점, HPA와 같이 사용하면 충돌 발생 가능

<br>

## 로컬 테스트

로컬 환경에서는 Metrics API를 이용하지 못할 가능성이 크다

https://github.com/kubernetes-sigs/metrics-server
위의 metrics-server github 들어가면 확인할 수 있는 명령어 실행해서 설치해준다.

```shell
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```
```shell
kubectl edit deployments -n kube-system metrics-server
```

HPA 확인

```shell
$ kubectl -n factorial get hpa               
NAME            REFERENCE                     TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
factorial-hpa   Deployment/my-factorial-app   3%/50%    2         5         2          40s
```
target 지정한 50% 중 현재 노드들에서 3% 사용 중이라고 나옴

<br>

## Redis 관련

실습에서 task를 Set에 담아두었다가 하나씩 pop해서 작업하는 것이 있는데
여러 개 파드에서 pop 연산이 atomic을 보장하는지를 잘 체크해야 함

```kotlin
redisTemplate.opsForSet().pop("factorial:task-queue")
```
redis의 Set pop 연산은 atomic을 보장한다.