# Kubernetes

- Tranditional Deployment
- Virtualized Deployment
- Container Deploymenet

<br> 

## :pushpin: kube 설정하기

### VM 환경설정

- vagrant, virutal box install
- [강의 installation doc 참고](https://github.com/joneconsulting/k8s/blob/master/install/kubernetes_install.md)
- [CentOS 7에서 docker 설정하기](https://yooloo.tistory.com/187)

### kube 설정

- kubeadm init 오류 발생시
```shell
$ kubeadm reset
$ kubeadm init --ignore-preflight-errors=all
```

``` shell
$ kubeadm join 192.168.32.10:6443 --token jftvpd.f9648oxxv0p21re3 \
	--discovery-token-ca-cert-hash sha256:2822ca8076c1a8750f690d9b491620b25ed6f1608b70899acb2dcc7ff93a8e02
```

### docker desktop의 minikube로 테스트 구성  
> **minikube**  
> kubernetes는 Master Node, Worker Node(한 개 이상)로 구성됨  
> Master Node의 일부 기느이과 개발 및 배포를 위한 단일 Worker Node를 제공해주는 간단한 kube 환경 제공해주는 것이 minikube  
> (단순한 개발 테스트를 위해 Node들을 구성하는 것 자체가 쉽지가 않아서 이런 식으로 테스트해볼 수 있게 제공해주는 듯)

