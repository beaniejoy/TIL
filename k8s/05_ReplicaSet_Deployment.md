# Replicaset & Deployment

```shell
$ kubectl apply -f my-simple-pod.yaml
pod/my-simple-pod created
$ kubectl apply -f my-simple-pod.yaml
pod/my-simple-pod unchanged
```
쿠베는 `Desired State <-> Current State` 비교하며 자동 Reconcilation 해주게 되는데 같은 이름으로 된 pod를 두 개 띄우고 싶다고 같은 명령어를 사용하면 기존 파드가 존재해서 변경할 것이 없기에 아무런 변화도 생기지 않는 현상 발생하게 된다.

쿠베가 객체를 식별하는 기준이 metadata의 name이기 때문
그렇다고 매번 다른 name으로 개별 설정한다면 관리측면에서 더 복잡해짐  

<br>

## ReplicaSet

여러 파드의 복제본을 생성하고 관리할 수 있는 객체  

위의 상황을 해결해줄 수 있도록 쿠베에서 또 다른 객체를 제공

목표수량(파드 몇 개 실행?)에 따라 파드 개수를 조정

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
    name: nginx-replicaset
spec:
    replicas: 3
    selector:
        matchLabels:
            app: nginx
    template:
        metadata:
            labels:
                app: nginx
        spec:
            containers:
            - name: nginx
              image: nginx:1.17
```
ReplicaSet 설정 내부에 Pod에 대한 설정 스펙도 같이 있음  
`spec.template` 에는 ReplicaSet에 들어갈 파드에 대한 설정 내용들이라고 생각하면 된다.

```yaml
selector:
    matchLabels:
        app: nginx
```
ReplicaSet은 본인이 관리하고 있는 Pod에 대해 식별할 수 있어야 하는데 그 때 Pod의 label을 사용

되도록 `selector.matchLabels`와 `template.metadata.labels`는 일치시키는 것이 좋다. 그래서 **ReplicaSet에 설정될 파드에 대해서는 고유의 label을 사용하는 것이 좋다.**  

레플리카 셋은 다수의 파드를 생성하고, 내부에서 그 숫자를 조정해주는 역할 담당

### 한계

spec에 정의한 image 버전을 올린다 하더라도 ReplicaSet은 이를 감지해서 반영해주지 않는다.  

수동으로 삭제한다면 ReplicaSet이 그 때 새로운 파드를 띄우게 되는데 그 때 새로운 버전이 적용된다고 보면 된다.  

> ReplicaSet 만으로는 버전관리가 어렵다는 단점이 있다. >> Deployment 존재이유

<br>

## Deployment

ReplicaSet의 버전을 관리해주는 객체

요 스펙을 작성하는 일들이 엄청 많을 것이다. (실제 실무에서도 그렇다. 후우)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: my-deployment
spec:
    # deployment 관련
    replicas: 3
    strategy:
        type: RollingUpdate
        rollingUpdate:
            maxSurge: 1
            maxUnavailable: 1
    # replicaSet 관련
    selector:
        matchLabels:
            app: my-app
    # pod 관련 내용
    template:
        metadata:
            labels:
                app: my-app
        spec:
            containers:
            - name: my-container
              image: nginx:1.17
```

strategy: pod 변경이 이루어져야 할 때 어떤 방식으로 할지 결정

### Deployment update 전략

- Recreate 전략
  - 모든 파드를 한 번에 교체하는 전략
  - 모든 파드를 종료하고 동시에 모두 새로 생성한다
  - 기존 파드를 종료하고 다시 모두 새로 생성되는 그 찰나의 순간이지만 그 순간에 애프리케이션 downtime이 발생하게 된다.
- **RollingUpdate 전략**
  - 각각의 파드를 순차적으로 업데이트하는 전략
  - 순차적으로 종료시키면서 새로운 버전의 파드를 교체하는 식으로 이루어짐
  - 무중단 update 장점이 있음


### RollingUpdate

```
pod 1.18    ---------|-------              |
pod 1.18    ---------|--------------       |
pod 1.18    ---------|---------------------|-------
pod 1.19             |---------------------|-------
pod 1.19             |       --------------|-------
pod 1.19             |              -------|-------
```
1.19 파드가 생성되면 중간에 파드가 4개였다가 기존 1.18 파드를 제거하는 순간 또 추가로 1.19 파드를 띄우고 하는 과정을 반복해서 순차적으로 업데이트 진행  

무중단 update가 가능하지만 Update 구간에서 이전버전과 신버전이 섞이게 된다.  
그래서 애플리케이션 개발시 이전버전과 호환이 되도록 개발하는 것이 중요

파드의 개수는 3개 > 4개 > 3개 방식으로 이루어짐

- 옵션
  - `maxSurge`: 업데이트 하는 동안 파드가 얼마나 더 생성될 수 있는지
  - `maxUnavailable`: 업데이트 동안 파드가 얼마나 줄어들 수 있는지
- 업데이트 구간 파드 개수: replicas - maxUnavailable ~ replicas + maxSurge
- 위의 deployment 스펙 예제에서는 각각 1로 설정되어 있는데 1개씩 신규버전 파드를 띄울 수 있고, 1개씩 이전 파드를 제거할 수 있다고 보면 된다.
- **두 개다 0이 될 수 없다.**
- 만약 업데이트 구간 동안 replica 개수가 줄어들지 않도록 하고 싶다면 maxUnavailable을 0으로 하면된다.
- 정수가 아닌 percentage로 지정가능 >> 두 값 모두 default로 25% (1/4 만큼씩 제거하고 신규 생성)

```shell
# 마지막 배포 상태를 조회
$ kubectl rollout status deployment/my-deployment

# 배포 기록과 revision 확인
$ kubectl rollout history deployment/my-deployment

# 배포를 지정한 이전 revision으로 롤백
$ kubectl rollout undo deployment/my-deployment --to-revision=2

# 전체 파드 현재 버전으로 재시작
$ kubectl rollout restart deployment/my-deployment
```
