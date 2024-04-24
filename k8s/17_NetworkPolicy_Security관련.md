# NetworkPolicy & ServiceAccount & RBAC

<br>

## NetworkPolicy

쿠베 클러스터 내부의 파드들은 제한 없이 서로 통신 가능  
일종의 내부 Private Subnet으로 묶여있어서 딱히 방화벽같은 설정도 없다.

NetworkPolicy 객체는 일종의 방화벽을 설정해주는 객체라고 생각하면 된다. 

AWS SecurityGroup과 비슷한 역할 수행

- Pod 혹은 네임스페이스를 기준으로 호출 가능 여부를 설정하는 객체
- ingress 규칙은 특정 Pod를 호출할 수 있는 네임스페이스와 Pod를 지정
- egress 규칙은 특정 Pod가 호출할 수 있는 네임스페이스와 Pod를 지정
- NetworkPolicy는 화이트리스트 방식이며 지정하지 않은 대상은 모두 호출이 거부됨
  - kube cluster는 all open 설정인데 NetworkPolicy 걸리는 순간 설정되지 않는 대상은 모두 거부

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: my-netpol
spec:
  podSelector:
    matchLabels:
      app: my-app # 화이트리스트 걸 대상을 기준으로
  ingress: # 대상이되는 pod로 들어오는 inbound 개념
    - from:
      - namespaceSelector:
          matchLabels:
            profile: dev
      - podSelector:    # or 조건으로 별개로 적용된다.
          matchLabels:
            role: gateway
      ports:
        - protocol: TCP
          port: 8080
```
- `ingress.from.namespaceSelector`
  - namespace 단위로 지정, 지정된 namespace 제외한 나머지는 대상 pod로 호출이 불가능
- `ingress.from.podSelector`
  - pod 단위로 연결을 지정해줄 수 있다.
  - 보통 service 객체의 selector는 deployment로 연결, NetworkPolicy도 deployment 단위로 생각하는 것이 좋다.
  - deployment 설정된 내용과 일치시키는 것이 좋을 것 같음
  - 바로 위 namespace가 `profile:dev`가 아닌 다른 곳에서의 pod에서 podSelector만 만족하면 호출가능
- `ports`:
  - 대상이 되는 pod의 8080포트만 호출가능, 다른 포트는 제외된다.
  - 범위로 지정 가능
  - 해당 포트 설정 안해주면, 모든 포트에 대해서 all open

```yaml
spec:
  egress:
    - to:
      - namespaceSelector:
          matchLabels:
            profile: production
        podSelector:    # and 조건으로 namespaceSelector 조건과 묶이게 된다.
          matchLabels:
            role: front
```

<br>

## ServiceAccount & RBAC

쿠베에 설정된 namespace를 아무나 지울 수 있다면 큰사고  
이런 불상사가 발생하지 않도록 어떤 객체들에 대해서 접근권한을 세세하게 제어할 수 있음

### Acccount 

- User Account: 사람이 사용하는 계정
  - ex. kubectl 명령어 통해서 어디까지 사용할 수 있는지
- Service Account: 애플리케이션이나 프로세스가 사용하는 계정
  - Pod내에 띄운 애플리케이션 kube api를 어디까지 이용할 수 있는지

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-service-account
  namespace: dev
```

### Role

- Role: 어떤 객체에 대한 작업 권한의 집합
  - pod 조회, 시크릿 조회, deployment 삭제 가능 등 각각에 대한 권한
- Cluster Role: 클러스터 전체에 영향을 주는 객체들에 대한 권한
  - node, pv, namespace 생성, StorageClass 등에 대한 권한

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```
role과 account는 namespace 단위로 생성되고 관리된다.  
권한도 화이트리스트 방식으로 동작

### RoleBinging

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: dev
subjects:
- kind: ServiceAccount
  name: my-service-account
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```
Account와 Role binding  
subjects로 되어있는데 role 하나에 여러 개 serviceAccount들을 묶을 수 있다.  
role 여러 개를 매핑하고 싶어서 RoleBinding 여러 개로 나오는게 좋은 practice가 아니다.  
그래서 다 포괄하는 새로운 Role을 만들어서 바인딩하는 것이 관리측면에서 더 좋을 수 있다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  namespace: default
spec:
  serviceAccountName: my-service-account
  containers:
  - name: my-container
    image: my-image
```
여기서 serviceAccountName에 지정된 serviceAccount에 따른 권한은  
해당 Pod 안에 어플리케이션(Spring, node.js 등)이 kube cluster api에 접근할 수 있는 권한을 의미하는 것임
(실행 중인 어플리케이션이 동적으로 kube api 접근할 때, 근데 잘 사용되는지는 모르겠음)

