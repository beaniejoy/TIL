# DaemonSet & StatefulSet

## DaemonSet

쿠베에서 Daemon의 역할을 수행하는 객체

파드 내부에 있는 백그라운드 프로세스를 일컫는 것이 아니라  
각 노드의 백그라운드 프로세스를 수행하는 각각의 파드라고 보면 된다.

각각의 노드마다 실행되는 모든 daemon 형태의 파드들을 DaemonSet이라고 부른다.

기본적으로 파드이기 때문에 필요에 따라 일부 노드에만 선택적으로 실행가능  

fluentd 같은 로그 수집기를 DaemonSet 이용해 구성 가능

```
Node01 - fluentd  pod  pod
Node02 - fluentd  pod
Node03 - fluentd       pod
```
fluentd 묶음을 DaemonSet이라고 할 수 있다.

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
    name: fluentd-daemonset
spec:
    selector:
        matchLabels:
            name: fluentd
    template:
        metadata:
            labels:
                name: fluentd
        spec:
            containers:
            - name: fluentd
              image: fluent/fluentd:latest
```
여러 파드를 관리한다는 점에서 deployment, replicaset과 유사

<br>

## StatefulSet

pod의 고유 아이덴티티를 보장해주는 객체

파드는 언제든 제거되고 새로 생성될 수 있도록 개별 객체의 고유한 아이덴티티는 약하게 설계되어있음(군집의 일부분일뿐)

쿠베는 또한 파드 하나를 직접 접근하기보다 라벨 등과 같이 군집을 이루어 selector의 선택을 받게 되는 형태로 구성

하지만 애플리케이션 구성 중에 workload가 꼭 동등한 레벨을 가지지 않는 경우가 존재
ex. db의 primary, replicas

쿠베에서 각 파드를 일부러 식별할 수 있도록 해주는 것이 StatefulSet

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
    name: redis-set
spec:
    replicas: 3
    selector:
        matchLabels:
            app: redis
    template:
        metadata:
            labels:
                app: redis
        spec:
            containers:
            - name: redis
              image: redis
```
`redis-set-1`, `redis-set-2`, `redis-set-3` 이런 식으로 순서대로 각각 고유의 네임이 지어진다.

```shell
$ kubectl scale statefulsets redis-set --replicas=2
```
마지막 `redis-set-3`이 제거됨

```shell
$ kubectl scale statefulsets redis-set --replicas=3
```
마지막 `redis-set-3` 이름으로 새로 생김

이렇게 파드의 아이덴티티가 정해짐  
이런 식으로 하면 CQRS에 의해 write, read를 분리해서 구성가능

하지만 보통은 stateless하게 운영할 수 있는 애플리케이션들을 쿠베로 관리하고 data storage가 필요한 redis, database 같은 것들은 클라우드 서버나 물리서버로 관리하게 된다.

> 실제 StatefulSet은 잘 사용되지 않는 객체다.
