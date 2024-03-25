# Service

쿠베는 물리적인 인프라 요소 위에 자체적인 네트워크 레이어를 만들어서 관리하고 동작하는 구조

```
outer requests
>>> (kube) ----
Ingress, Service(NodePort), Service(LoadBalancer)
>>>
frontend
>>>
Service(ClusterIP)
>>>
backend
>>>
Service(ExternalName)
>>> (kube) ----
database
```
Service를 쿠베에서 발생할 수 있는 네트워크 종류들을 모두 추상화한 객체들, 네트워크랑 같은 단어라고 생각하면 된다.

```
Ingress, Service(NodePort), Service(LoadBalancer)
```
- Ingress: 외부에서 내부 호출을 컨트롤하는 일종의 reverse proxy 역할 담당
  - **외부에서 들어오는 트래픽을 담당하는 경우에는 Service보다 Ingress를 더 많이 사용**
- Service(NodePort): 특정 포트와 파드를 연결
- Service(LoadBalancer): cloud에서 제공하는 load balancer와 쿠베 객체를 연결

```
Service(ClusterIP)
```
파드 간에 통신하기 위해 사용되는 객체  
서비스 자체가 클러스터의 내부 ip를 가질 수 있도록 해준다.  
이걸 통해 파드를 호출할 수 있도록 핸들링 해주는 객체

```
Service(ExternalName)
```
쿠베 클러스터에서 >> 외부 호출하는 경우

도메인의 CNAME과 비슷하게 동작, 어떤 특정 도메인 주소를 서비스에 매핑하면 지정한 도메인 주소로 트래픽이 갈 수 있도록 해준다.  

```
Service(Headless)
```
vip, headless, 수작업으로 목적지를 관리?

<br>

## ClusterIP 타입 Service

```
# pod 기준
frontend >> backend(app=my-app)
```
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ClusterIP
  selector:
    app: my-app
  ports:
  - protocol: TCP
    port: 8080
```
- `type: ClusterIP`: Service 객체에서 가장 많이 사용되는 타입, **그래서 생략도 가능**
- `selector`: 해당 서비스가 어떤 파드를 대상으로 할 것인가 설정
  - 이 서비스를 호출하면 selector에 설정한 파드들로 트래픽이 분배되도록 해준다.

**EndpointSlice**

Service가 라우팅되는 파드들의 ip 주소를 가지고 있는 객체  

```
frontend >> backend(1), backend(2), backend(3)
```
같은 이름과 라벨의 여러 파드들의 ip 주소들을 관리, 파드가 줄어들고 생성될 때 EndpointSlice 내용 update  
**service 호출하면 EndpointSlice 기준으로 라우팅된다.**

생기는 것도 자동으로, 안에 내용 update도 자동으로 되기 때문에 개발자가 건들일은 거의 없다.  
(Headless Service나 Service의 selector가 없어서 수동으로 설정해야될 때 건드는 일은 있다.)

**port**
서비스 호출할 때 어떤 port로 호출해야하는지 알려주는 부분  

```shell
curl http://my-service:8080
```
target이 되는 Pod의 targetPort가 설정되지 않으면 Service에서 지정한 port로 트래픽이 target pod로 라우팅이 된다고 보면 된다. (위에는 8080:8080포트로 라우팅)  
만약 targetPort를 3000번으로 등록하면 8080:3000 이렇게 포워딩된다고 보면 된다.

<br>

## 보통의 클러스터 Service 구성

```
Service -> Deployment > ReplicaSet > Pod, Pod
```

```yaml
apiVersion: v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: my-app
```
deployment에서 selector 설정과 Service 객체의 selector를 연결하게 된다.  
**보통 그래서 Service(ClusterIP), Deployment 간에 1대1 매핑을 하게 된다.**  
Deployment는 특정 애플리케이션을 대표하게 된다.  

<br>

## domain 주소

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ClusterIP
  selector:
    app: my-app
  ports:
  - protocol: TCP
    port: 8080
```
metadata.name에 지정하면

```shell
curl http://my-service:8080
curl http://my-service.default.svc.cluster.local:8080 # FQDN
```
FQDN으로 사용할 수도 있는데, **service와 호출하는 pod와 같은 namespace에 있으면** my-service 뒤에 있는 내용은 생략 가능

<br> 

## 그외 요소들

### SessionAffinity

특정 ip에 서비스를 고정해서 호출하고 싶다할 때 사용가능  
근데 비추천

```yaml
spec:
  #...
  sessionAffinity: ClientIP
  sessionAffinityConfig:
    clientIP:
      timeoutSeconds: 600
```

<br>

## ExternalName Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-external-service
spec:
  type: ExternalName
  externalName: my.database.example.com
```
```shell
curl http://my-external-service:3306
curl http://my.database.example.com:3306
```
두 개의 curl은 같은 내용으로 호출된다.  
저걸 도입하면 좋은 부분은 external 서버의 도메인주소가 변경되면 해당 Service 객체 내용만 수정하면 된다.  
하지만 DNS 캐쉬를 고려한다면 실시간 반영이 아니기 때문에 주의해야 한다.  
(클러스터 자체 캐시, 파드 내 캐시, 애플리케이션 자체 내 캐쉬 등 여러 군데에 캐쉬가 존재할 수 있다.)
