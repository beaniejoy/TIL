# StorageClass & Dynamic Provisioning

동적 provisioning이 쉽게 달성될 수 있는 것이 아니긴 하다.

볼륨을 필요한 시점에 필요한 만큼 생성하여 할당하는 기술

클라우드 서비스와 같이 사용하는 경우 자연스럽게 구현 가능

object storage(AWS S3..) 같은 경우는 DP에 어울리지 않는다. 파일을 객체로 관리하는데 반해 DP는 mount해서 관리하기 때문에 파일시스템 같은 형태가 적합하다고 할 수 있다.  

- Static Provisioning
```
container ----> PVC -- (claim) -- > PV(정적으로 미리 만들어둔 PV)
PV -- (bound) --> PVC -- (mount) --> container
```

- Dynamic Provisioning
```
container ----> PVC -- (claim) -- > ??(사전에 미리 준비해둔 PV는 없다.)
?? -- (bound) --> PVC -- (mount) --> container
```
요청이 들어올 때 저장공간을 만들어서 제공 (그럼 어떤 스펙으로 만들어줄 것인가? >> **StorageClass에서 저장공간에 대한 스펙 정의**)

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ebs-storage
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp3
```
- `provisioner`: 저장공간을 만들어줄 스토리지가 어떤 서비스를 사용하는지 명시  
- `parameters`: provisioner에 따라 다르다.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-storage
provisioner: rancher.io/local-path
volumeBindingMode: WaitForFirstConsumer
```
- `provisioner: rancher.io/local-path`: 로컬 스토리지 활용(테스트 상황에서만)
- `volumeBindingMode`: 스토리지를 실제로 만들어줄 타이밍을 언제로 할 것인지(provisioner 종류 관계없이 설정 가능)
  - `Immediate`: PVC가 StorageClass에 claim을 할 때 즉시 생성
  - `WaitForFirstConsumer`: PVC가 파드나 container에 mount될 때(PVC가 생성되어 claim되는 순간에는 Pending 상태였다가 mount 되는 순간 실제 생성)

<br>

## WaitForFirstConsumer 존재 이유

EBS같은 경우 자신이 속한 가용영역(Availablity Zone)이 존재하는데 해당 노드와 같은 가용영역에 있는 경우에만 EBS 접근 가능하고 다른 영역에 있다면 EBS가 붙을 수 없다.

보통 쿠베 클러스터를 클라우드 서버에서 구축하는 경우 HA를 고려해서 여러 노드가 여러 가용영역에 퍼져있는 경우가 대부분. 이런 경우 EBS같은 경우 자신이 실제로 연결될 Pod가 어떤 가용영역에 떴는지, 다시 말해 어떤 노드에 떴는지 알아야 그 가용영역에서 volume을 만들어 attach를 할 수 있게 된다.

하지만 쿠베 스케줄링에 의해 Pod가 생성되고 난 이후에 어떤 노드에 속하게 되는지 결정되는데 **실제로 저장소를 provisioning하는 것도 파드가 생성된 이후에 수행 가능**

PVC가 먼저 만들어져야 Pod 스펙에 PVC 정의가능해지고 Pod를 생성할 수 있게 된다.  
**PVC가 파드보다 먼저 생성되어야 하는데 실제 볼륨은 파드가 생성된 이후에 생성되어야 하기 때문에 모순**

그래서 실제 PV 생성시점을 지연하도록하는 설정이 추가(`WaitForFirstConsumer`)

<br>

## PVC 설정 (StorageClass 연결)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-dynamic-pvc
spec:
  storageClassName: local-storage 
accessModes:
  - ReadWriteOnce
resources:
  requests:
    storage: 100Mi
```
`storageClassName`: 명시적으로 StorageClass를 지정하고 싶을 때  
selector 같이 조건에 맞는 StorageClass를 지정할 수도 있는 것 같음

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
      claimName: my-dynamic-pvc
```
요거 역시 pvc는 추상화된 형태이기 때문에 pod 입장에서는 신경쓰지 않아도된다.

```yaml
apiVersion: apps/v1
kind: StatefulSet
...
    spec:
      containers:
      - name: redis
        image: redis
        volumeMounts:
        - name: dynamic-volume
          mountPath: /tmp/dynamic
  volumeClaimTemplates:
  - metadata:
      name: dynamic-volume
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: local-storage
      resources:
        requests:
          storage: 100Gi
```
위와 같이 StorageSet에도 dynamic volume을 설정할 수 있고
`StatefulSet-0`, `StatefulSet-1`, ... 이런 식으로 새로 생성될 때마다 스펙에 정의한 volume대로 새로 저장공간을 생성해서 할당을 받게 된다.

고려해야할 것이 DB같은 것을 StatefulSet으로 사용하게 되면 해당 파드가 떠있는 노드와 볼륨 스토리지와 같은 가용영역안에 존재해야하는데 pod가 내려가고 다시 뜰 때 완전히 다른 노드에 뜰 수가 있다.  
이렇게 되면 기존 볼륨 스토리지와 전혀 다른 볼륨 스토리지에 동적으로 할당 받게 되는 불상사가 발생할 수 있다.

이것을 방지하려면 selector, affinity 같은 것들을 통해 특정 가용영역, 노드에 고정되어 파드가 뜰 수 있도록 설정하는 것이 좋다.
(혹은 EFS같은 스토리지 서비스를 사용하는 것도 고려해볼 수 있다. 참고만)

`volumeClaimTemplates`은 StatefulSet에서만 사용 가능

<br>

## 궁금증

PV 객체를 설정할 때 hostPath로 설정하게되면 **어떤 디렉토리가 PV로 사용되는 것인지 궁금**