# PersisentVolume & PersisentVolumeClaim

## PersisentVolume

Pod의 라이프사이클과 관계없이 영구적으로 보존되는 저장소 

일반적으로 노드와 무관하게 Pod에 연결될 수 있는 저장장치  
(클라우드 스토리지 서비스, 네트워크 스토리지 등)

ephemeral volume은 쿠베 내부에 공간을 만들어서 container에 mount해서 사용하는 형태  

```
type: NFS  
capacity: 500Mi
```
persistent volume(PV)은 실제 공간 설정(**Static Provisioning**)

### Static Provisioning  

> pod, container 등이 생성되어 실행되는 동안에 저장공간의 할당이 이루어지는 것이 아니라 pod 스펙을 정의할 때 이전에 이미 만들어둔 저장공간 존재

```
type: NFS  
capacity: 500Mi

type: EBS
capacity: 200Mi

type: EFS  
capacity: 1Gi
```
미리 여러 PV를 만들어둔 상태로 container에 적당한 저장소를 선택해 mount

하지만 Static Provisioning은 유연하게 동작하는 쿠베의 성격과 좀 맞지 않은 부분이 있어보임  
예를 들어 3개의 PV를 구성했는데 컨테이너는 그보다 많은 개수로 구성을 해야되어 더 많은 PV를 필요로 할 때  
(scale에 취약)

### Dynamic Provisioning

위의 Static한 방식의 부족한 부분에 대해서 유연하게 PV를 사용하기 위해 등장

volume 저장공간을 미리 만들어두는 것이 아니라 필요할 때 원하는 만큼의 저장공간을 생성해서 연결  
(container 생성시 500Mi 만큼 저장공간이 필요하다면 그 때 해당 용량만큼 생성해서 mount하는 방식으로 진행)

<br>

## PersistentVolumeClaim

**PVC**라고 함

PV는 컨테이너에 바로 마운트해서 사용하기 애매한 부분이 존재  
컨테이너의 스펙을 정의하는 시점하고 영구볼륨 준비하는 시점이 다르기 때문에 컨테이너에서 어떤 볼륨을 사용하겠다 하는 것이 어려울 때가 있음(name 지정)  

**dynamic provisiong 같은 경우 container 스펙을 작성하는 정적 타이밍에 볼륨자체가 없기 때문에 지정하는게 어려울 수 있다. (이게 이해가 안가지만 우선 그런 이유가 있다정도만 기억할 것)**

정적인 경우 원하는 스펙의 저장공간을 요청해서 받아야하고, 동적인 경우 미리 준비된 것이 없기 때문에 원하는 스펙의 저장공간을 만들어달라고 요청해야할 필요가 있다. 

요걸 별도 객체로 분리한 것이 `PVC`

```
container >> PVC >> (request) >> Storage(Dynamic or Static)
```
컨테이너가 필요로하는 볼륨 저장공간에 대한 스펙을 요청하면 PVC가 그걸 대신해서 request를 Storage에 보내게 된다. 

Storage는 Dynamic 혹은 Static한 방식 중 적절한 것을 택하여 반환하게 되고 **container는 PVC를 mount하게 된다.**

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv-500
spec:
  capacity:
    storage: 500Mi
  accessModes:
  - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: "/my-pv/data/pv1"
```
- `capacity.storage`: 실제 저장공간의 용량을 고려해서 작성해야한다.
  만약 실제 저장공간 용량보다 더 크게 잡으면 예기치않은 data loss 혹은 에러 발생가능
- `accessModes`: 여러 파드가 하나의 볼륨객체에 접근할 수 있다. 이와 관련된 설정
  - ReadWriteMany(RWX): 여러 노드의 여러 파드에서 접근, 쓰기 가능
  - ReadOnlyMany(ROX): 여러 노드의 여러 파드에서 접근(읽기)만 가능
  - ReadWriteOnce(RWO): 하나의 노드에서만 접근, 쓰기 가능(하나의 노드에 여러 파드인 경우 모두 접근 가능)
  - ReadWriteOncePod(RWOP): 하나의 파드에서만 접근, 쓰기 가능
- `persistentVolumeReclaimPolicy`: 사용이 끝난 PV를 어떻게 할 것인지
  - PVC 자체의 라이프사이클이 끝나는 경우가 존재하는데 그 때 PV는 더이상 사용이 안됨
  - 만약 이전에 사용했던 데이터들이 다른 PVC에서 접근하는 경우 영향을 줄 수 있는데 해당 정책으로 설정가능
  - `Retain`: PV 내부 내용 유지
  - `Delete`
    - PV 라이프사이클 종료시 저장공간 자체에 대한 자원할당을 아예 해제(내용 삭제개념과 다름)
    - EBS(클라우드 제공 volume)같은 경우 저장공간 자체를 release 해버림, 다시는 사용 불가
  - `Recycle`: 공간 내부에 데이터들을 삭제 초기화 한 후, PVC 요청이 왔을 때 해당 PV를 할당할 수 있도록 해줌, **하지만 deprecated**
    - 저장공간을 쓰고 반납하고 다시 쓰고하는 방식으로 재활용해서 사용하고 싶다면 Dynamic Provisioning 방법을 사용하는 것이 좋다.
- `hostPath`
  - 볼륨의 실제 저장공간 디렉토리를 지정해주는 역할
  - 컨테이너가 실제 실행중인 노드의 특정 디렉토리를 저장공간으로 사용하도록 지정
  - 임시볼륨에도 있던 저장공간 스펙이라 보통 PV를 사용할 때는 영구볼륨으로는 역할을 할 수 없기에 사용 X
  - **다만 노드가 딱 하나인 경우, 노트북에서 개발테스트하는 경우에 사용**
    - hostPath를 통해 실제 스토리지 서비스 세팅 없이 PV를 생성할 수 있다.
  - 운영환경에서는 쓰지 말자

```yaml
# hostPath 대신
nfs:
  server: "<nfs-server-ip>"
  path: "/path/to/share"

# EBS
awsElasticBlockStore:
  volumeID: "<aws-volume-id>"
  fsType: ext4
```
위와 같이 저장공간 설정(공식 문서보고 설정해야함)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc-100
spec:
  accessModes:
  - ReadWriteMany
resouces:
  requests:
    storage: 100Mi
```
selector를 이용해 PV를 지정할 수 있다.  
accessModes와 selector가 있다면 할당 가능한 PV 중에 후보군 필터링 후  
request storage 용량보다 많은 PV를 선정

```
PV(250Mi), PV(500Mi), PV(1000Mi)
```
여러 후보군이 있을 때 가능한 가장 작은 PV를 선정해준다.(더 큰 낭비를 방지하기 위해)

만약에 PVC request에 만족하는 PV 후보군이 없다면 Pending 상태에 머물게 된다.

```yaml
# Pod spec
spec:
  containers:
  - name: my-container
    image: nginx
    volumeMounts:
    - name: my-storage
      mountPath: /data
  volumes:
  - name: my-storage
    persistentVolumeClaim:
      claimName: my-pvc-100
```
만약 영구볼륨 내용을 수정하려고 한다면 PVC 혹은 PV의 스펙내용만 변경하면 되고 Pod 단위에서는 PVC를 인터페이스 처럼 추상화된 것을 사용만 하면 되기에 유지보수에 좋다. (Pod 내용은 수정 안해도 되기에)

<br>

## 정적 프로비저닝의 활용

Pod의 스케일을 고려하면 정적 프로비저닝의 사용은 약간 제한적이다.

ReadWriteMany or ReadOnlyMany 형태의 공유 저장소로 활용  
**파드마다 독립적인 저장소가 필요하다면 임시 볼륨의 활용이 적합함**

만약 DB처럼 파드 단위로 할당되는 영구 스토리지가 필요하다면 StatefulSet 활용  
(하지만 이렇게도 잘 사용 X)