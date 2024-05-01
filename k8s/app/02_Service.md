# Service 관련

## Ingress 

nginx controller 설정

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: user-ingress
  annotations:
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  rules:
    - http:
        paths:
          - pathTyep: ImplementationSpecific
            path: /user(/|$)(.*)
            backend:
            #...
```
nginx의 rewrite 기능을 사용하고 싶을 때 위와 같이 사용 가능

```
/user/users/1004 >> /users/{userId}
```

<br>

## 애플리케이션의 네트워크 설정

- 애플리케이션을 서비스와 대응하는 형태로 설정
service - deployment 1대1 매핑하는 것이 좋다.
애플리케이션 호출하기 위해 service를 호출하는 식으로
여러 애플리케이션에 요청 전달이 필요하다면 하나의 service에서 selector를 통해 여러 deployment를 묶는 방식보다 message queue를 이용해 consume 하도록 하는 것이 좋다.

- 기본적으로 같은 클러스터에서는 모든 애플리케이션이 서로 통신이 가능
네임스페이스 유무 상관없이 호출 가능
클러스터 규모가 커지면 network policy 객체를 통해 제한을 하는 것이 좋다.
dev, prod 구성할 때 클러스터를 따로 구성하는 것이 좋다. (리소스 관리 측면에서도)

- 세션에 의존하기 보다 모든 요청이 독립적으로 이루어질 수 있도록 구현
(session affinity??를 통해 k8s에서 sticky session 구현 가능. **but, 이렇게 안하는 것이 좋다**)

- 호출 대상의 스케일이나 가용성을 고려하지 말고, 호출 자체에 집중
클라이언트 단에서 호출 대상에 대해 깊이 생각할 필요 없이 그냥 알아서 잘하겠거니 하고 호출만 하는 것에 집중할 것 (k8s에 맡기자)

- 쿠베 네트워크 기능이 충분하지 않다면 별도 플러그인 고려할 수 있음
load balancing 알고리즘, circuit breaker 같은 것들을 별도 플러그인의 지원을 받을 수 있다.

<br>

## kind ingress 구성

```shell
kind delete cluster
```
기존 kind cluster 제거

https://kind.sigs.k8s.io/docs/user/ingress

여기서 create cluster 이후 Ingrss NGINX 설정하면 된다.