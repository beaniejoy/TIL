# Namespace

쿠베 클러스터를 사용하다보면 여러 개발자들이 하나의 클러스터를 사용하면서 이런 저런 객체들을 잘못 건드리는 일이 발생할 수 있다. 그래서 영역을 명확하게 구분하여 profile 환경 별로, 개발팀 별로 구분을 지어 격리하도록 할 수 있는데 그 단위를 쿠베에서 Namespace로 제공하고 있다.

```
kubernetes

dev - pod, service, secret, pvc
stage - pod, service, secret, pvc
production - pod, service, secret, pvc

> 서로 다른 namespace에서는 다른 객체들을 원천적으로 접근할 수 없다.
```

주의할 점이 쿠베 클러스터 상에서 논리적으로 구분을 해주는 것이지 실제 node에는 구분이 안되어 있다. (namespace는 오로지 논리적인 구분)

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: my-namespace
```

```shell
$ kubectl create namespace my-namespace
```
namespace는 예외적으로 명령형으로 생성하는 경우가 많다.  

쿠베의 객체간에는 순서가 중요하지 않다. Pod가 먼저 생성되든, Service가 먼저 생성되든 상관이 없다.

하지만 namespace는 다른 객체가 생성되기 전에 먼저 생성이 되어야 한다. (명시적으로 가장 먼저 생성되기 위해)  
그렇다고 선언형 방식으로 yaml 파일로 적용하는게 틀리다는 것은 아니다. 다만 다른 적용될 객체들을 생성하기 전에 namespace를 가장 먼저 생성하는 것이 중요하다.

```shell
$ kubectl delete namespace my-namespace
```
namespace를 삭제할 때 특별히 주의할 것이 네임스페이스에 속한 모든 객체가 같이 삭제된다. 그래서 특별히 주의를 요하는 명령어다.

쿠베 각 객체에 metadata.namespace로 지정 가능  
만약 생략하면 `default` namespace로 설정된다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-simple-pod
  namespace: dev # 생략하면 default
spec:
  containers:
  - name: my-container
    iamge: nginx
```
default는 특별한 목적을 가지고 사용하지 않는 것이 좋다.  
namespace를 따로 지정하는 것이 좋은데, 개발자 실수로 default로 생성되는 객체들이 많이 쌓이게 된다. 그래서 실제 사용되는 것과 사용하지 않는 것들이 혼재되어 관리의 어려움이 존재  

**실서비스는 별도 구분된 namespace 안에서 사용하는 것이 중요하다.**

```shell
$ kubectl -n dev get pods
```
`-n, --namespace` 옵션을 통해서 특정 namespace에 있는 객체만을 조회할 수 있다.  
만약 해당 옵션을 생략하면 기본적으로 default namespace를 바라보게 된다.

```shell
$ alias kd='kubectl -n dev'
```
alias로 따로 지정해서 사용 가능

```shell
$ kubectl get pods --all-namespaces
```
모든 namespace에 대한 객체 조회

그리고 namespace가 다르다고 객체간의 커뮤니케이션이 완전히 닫힌 것은 아니다.

```shell
dev - pod
production - my-service

# dev pod에서 production my-service 호출하고자 할 때
$ curl http://my-service.production.svc.cluster.local:8080
```
FQDN을 통해 다른 namespace에 있는 서비스를 호출할 수 있다.  
(namespace 간의 호출은 기본적으로 열려있다.)
