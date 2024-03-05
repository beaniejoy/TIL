# docker 로컬 실행환경 구성

> mac os 기준

- [docker desktop](https://www.docker.com/)
- https://kind.sigs.k8s.io
  - 로컬 환경에서 쿠베 클러스터 환경을 구성해주는 역할

```shell
$ brew install kind
$ kind create cluster
```
- kubectl 설치 (https://kubernetes.io/docs/tasks/tools/)

```shell
$ brew install kubectl
```

<br>

## Test

```shell
kubectl run nginx --image nginx
kubectl get pods nginx
kubectl delete pods nginx
```
kube에 nginx container 띄워달라는 명령어

