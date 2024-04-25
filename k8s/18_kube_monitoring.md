# kube monitoring

```shell
$ kubectl version
Client Version: v1.29.2
Kustomize Version: v5.0.4-0.20230601165947-6ce0bf390ce3
Server Version: v1.29.2
```
Kustomize: 쿠베가 배포를 위해 사용되는 도구

client version은 kubectl 버전  
server version은 kubectl에 연결되어 있는 kube cluster 버전  
둘 사이에 차이가 너무 나지만 않으면 된다.


- kubectl get

```shell
$ kubectl get pod my-pod -o yaml[json]
$ kubectl get pod my-pod -o wide
```
`-o` output option

- kubectl describe

객체들의 목록 혹은 개별 객체의 정보 확인
```shell
$ kubectl describe pod my-pod
```

- kubectl logs

컨테이너 내부의 로그를 확인
```
$ kubectl logs my-simple-pod
$ kubectl logs my-simple-pod -c nginx
```
`-f` 옵션 주면 `tail -f` 처럼 계속 추적해서 로그 확인 가능  
multi container 환경에서는 `-c` 옵션으로 container name을 주면 된다.  
컨테이너가 하나인 경우 따로 `-c` 옵션 없어도 된다.

- kubectl exec

쿠베에서 실행중인 컨테이너 내부로 진입하는 명령어

```shell
$ kubectl exec -it my-pod -- bash
```

- kubectl run

쿠베에 임의의 컨테이너를 바로 실행해서 사용

```shell
$ kubectl run -it --rm redis-pod --image=redis -- sh
```
파드를 생성해서 바로 컨테이너로 진입하게 되는데 --rm 옵션을 통해 해당 쉘 기능 종료하면 파드 종료되도록 설정할 수 있다.  
redis-server가 제대로 동작하는지 cli 통해 확인해보고자 할 때 임시의 파드를 띄워 내부에 컨테이너에서 redis-server 접근 체크할 수 있다.  

주로 연결 확인을 위해 사용(네트워크 이슈 등)

- kubectl get events
- kubectl diff

쿠베 클러스터에서 발생한 이벤트 로그의 조회

```shell
$ kubectl diff -f my-simple-pod.yaml
```
yaml 변경에 따른 차이 확인 가능(**좀더 확인해볼 것**)

- kubectl edit

쿠베 객체들을 직접 변경해서 바로 반영 (실행중인 객체에 변경)

```shell
$ kubectl edit pod my-simple-pod
```
다만 변경 추적이 어려워서 되도록 사용하지 말고 급한 경우에 사용