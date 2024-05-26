# Helm

쿠베를 위한 패키지 매니저
배포를 위한 패키지를 만들어 주는 도구
쿠베에 다양한 객체들, 다양한 yaml 파일이 존재하게 되는데 이거를 하나의 패키지로 관리할 수 있게 해주는 도구
(gradle, maven 같은 도구)

<br>

## Helm Chart 기본 구성 요소

- Chart Metadata: Chart의 기본적인 정의

```yaml
apiVersion: v2
name: my-chart
description: hello
type: application
version: 0.1.0
```

- Template: k8s 객체를 만들어내기 위한 템플릿

```
deployments.yaml
configmap.yaml
secret.yaml
service.yaml
ingress.yaml
```

- Value: 템플릿의 변수들에 들어갈 값

```yaml
replicaCount: 2
service:
  type: ClusterIP
  port: 8080
ingress:
  enabled: false
```

- Subchart: 해당 차트와 Value를 공유할 하위 차트들

```
Value, Template >> Chart >> Release(k8s cluster)
```
Value, Template을 명시하면 Helm Chart에서 이를 말아서 쿠베 클러스터에 배포하면 Release 형태로 객체들이 생성된다.

<br>

## install

```shell
brew install helm

helm create hello-chart
```