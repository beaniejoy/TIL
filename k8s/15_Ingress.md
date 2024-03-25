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

targetPort 30007번 포트로 지정하면 kube cluster **내부의 모든 node들에** 30007번 포트로 유입이 되면 대상이 되는 pod로 알아서 라우팅해준다고 생각하면 된다.

nodePort 명시되어 있지 않으면 쿠베가 알아서 남는포트번호 중에 아무거나 할당을 해준다. 그렇다고 아무거나 해주는 것은 아니고 **30000 ~ 32767** 사이의 포트 중에서 할당해준다.

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

kubernetes 외부에서 들어오는 트래픽을 내부 서비스로 연결해주는 리버스 프록시