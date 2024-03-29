# Ingress

외부에서 쿠베 클러스터로 호출할 때 처리하는 객체가 많이 존재

`Ingress`, `Service(NodePort)`, `Service(LoadBalancer)`

<br>

## Service(NodePort)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nodeport-service
spec:
  type: NodePort
  selector:
    app: my-api-gateway
  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30007
```
- `nodePort`: 외부에서 노출되는 포트
- `port`: 클러스터 내부에서 노출되는 포트
- `targetPort`: 어떤 pod의 어떤 port로 라우팅해줄 것인지 지정

`nodePort` 30007번 포트로 지정하면 kube cluster **내부의 모든 node들에** 30007번 포트로 유입이 되면 대상이 되는 pod로 알아서 라우팅해준다고 생각하면 된다.

`nodePort` 명시되어 있지 않으면 쿠베가 알아서 남는포트번호 중에 아무거나 할당을 해준다. 그렇다고 아무거나 해주는 것은 아니고 **30000 ~ 32767** 사이의 포트 중에서 할당해준다.

<br>

## Service(LoadBalancer)

`type: LoadBalancer` 인 것만 제외하면 ClusterIP Service 내용과 똑같다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-serivce
spec:
  type: LoadBalancer
  selector:
    app: myapp
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
```
네트워크 모든 설정들을 쿠베 내부에 위임하는 것이 힘든 경우 외부 loadbalancer service를 따로 둘 수 있다. (잘 사용은 안한다.)

하지만 위의 2개의 객체만으로는 아쉬운 점이 있었음.  
네트워크 레벨에 한정되어 있고 애플리케이션 레벨의 처리에 있어서 기능의 한계가 존재  
(path 기준으로 분기 처리, ssl 인증서 처리 등)

<br>

## Ingress

kubernetes 외부에서 들어오는 트래픽을 **내부 서비스**로 연결해주는 리버스 프록시

nginx 혹은 L7 loadbalancer(ALB) 하던 동작을 ingress에서 동작하도록 할 수 있다.  

ingress의 타겟은 파드가 아닌 서비스라는 점에서 장점이 있다.  
하지만 HTTP, HTTPS 트래픽만 처리할 수 있다는 한계가 존재한다.  
(그 외의 포트에 대해서는 NodePort, LoadBalancer 타입의 Service 객체들로 설정해야한다.)

동작을 정의하기 위한 **리소스**와 이를 처리하기 위한 **컨트롤러**로 구분  
보통은 어떤 path로 들어오면 어떤 서비스로 라우팅해줘 같은 인그레스 리소스를 주로 정의할 것이고 컨트롤러 같은 경우는 쿠베 설치할 때 같이 세팅되기에 건들일이 없다.  
ingress controller는 default 설정으로 nginx를 사용, cloud 같은 경우 ALB 같은 것들을 사용할 수 있다. 쿠베 내부에서 정의해서 사용

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  tls:
  - hosts:
      - myapp.example.com
    secretName: myapp-tls
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /user
        pathType: Prefix
        backend:
          service:
            name: user-service
            port:
              number: 8080
      - path: /product
        pathType: Prefix
        backend:
          service:
            name: product-service
            port:
              number: 8080
```

- `pathType: Prefix`: 문자열 그자체의 startsWith 같은 개념이 아니다. 경로상의 prefix  
  - `example.com/user/list`: 조건 부합  
  - `example.com/user-list`: 조건 부합 X (startsWith 개념이 아니다.)


<br>

