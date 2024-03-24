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
