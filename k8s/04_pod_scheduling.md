# Pod Scheduling

Control Plane의 Scheduler에 의해 수행  
스케줄러가 어떤 노드에 파드를 배치할 것인지 결정

스케줄링은 아래의 과정을 거치게 됨
- filtering
  - 파드를 배치할 수 없는 노드를 걸러내는 과정
  - 쓸 수 없는 노드들을 걸러내는 작업
- scoring
  - 필터링 되지 않은 노드에 대해서 어떤 노드가 파드를 배치하기에 적합한지 결정
  - 노드들 중에 scoring을 통해 가장 적합한 노드를 결정하고 거기에 파드를 배치

스케줄링은 쿠베의 고유 영역, 쿠베가 내부적으로 알아서 수행  
하지만 우리가 원하는 방식대로 배치가 이루어지지 않을 수 있다.  
ex) GPU가 있는 Node 2에 ML 관련 pod를 배치하고 싶은데 쿠베는 Node 2에 GPU 리소스가 있다는 것을 인지하지 못할 수 있다.  

위와 같은 경우를 위해 쿠베는 개발자에게 scheduler의 선택에 영향을 줄 수 있는 방법들을 제공해주고 있음  

<br>

## Node Selector

파드가 배치될 노드를 selector를 이용해 결정

```shell
$ kubectl label node node-02 hw-type=gpu
```
위와 같이 특정 노드에 label를 생성할 수 있음

```yaml
apiVersion: v1
kind: Pod
# ...
spec:
    nodeSelector:
        hw-type: gpu
        cost: high
    containers:
    - name: my-container
      image: ai-learning:latest
```
위와 같이 정의하면 해당 Pod는 hw-type이 gpu인 라벨이 붙여진 노드에 배치된다.

selector에 여러 개의 내용을 설정하면 and 조건으로 묶이게 됨(모두 가지고 있는 노드에 배치)

만약 selector 조건에 해당하는 노드를 찾지 못하면 해당 pod는 pending 상태에 계속 머무르게 된다. (**주의해야할 점**)

`nodeSelector`가 없는 파드에 대해서는 모든 노드는 후보군이 된다.  

<br>

## Taint and Tolerations

### Taint

파드가 배치될 수 없는 노드를 지정함

```shell
$ kubectl taint node node-02 hw-type=gpu:NoSchedule
```
label하고 유사하지만 실제 label이 지정되는 것은 아니고 노드에 속성이 추가된다라고 보면 된다.

- `NoSchedule`: 파드가 스케줄되지 않음(하드한 설정)
- `PreferNoSchedule`: 되도록 파드를 배치하지 않음
  - 다른 노드가 가득차서 해당 taint가 걸린 노드에 밖에 배치될 곳이 없다면 해당 노드에 배치된다.(소프트한 설정)
- `NoExecute`: 이미 있는 파드도 모두 제거함
  - NoSchedule 설정은 포함하면서 이미 존재하고 있던 파드가 해당하지 않은 파드라면 실행중이더라도 제거(아주 강력한 설정)
  - 제거되더라도 쿠베에 의해 다른 노드에 파드가 뜰 것이다.

### Tolerations

Taint만 있는 경우 특정 노드에 파드가 아예 배치되지 않을 수 있다.  

어떤 파드는 특정 노드에 배치되길 원하고 다른 노드는 배제하는 식으로 설정하고 싶다면 Toleration을 사용하면 된다.

```yaml
spec:
    tolerations:
    - key: "hw-type"
      operator: "Equal"
      value: "gpu"
      effect: "NoSchedule"
    containers:
    - name: my-container
      image: ai-learning:latest
```
해당 파드는 taint를 무시할 수 있도록 설정됨  
label은 key, value만 가지고 있지만 taint는 effect까지 가지고 있기에 같이 지정해줘야 한다.  

node selector 처럼 모두 일치할 때만 동작

tolerations는 taint를 무력화하는데에만 사용되는 것이지 해당 노드에 배치되기를 기대할 수는 없다. 다른 노드에 배치될 수 있다.

**nodeSelector, taint, tolerations를 모두 짬뽕해서 사용해야 특정 노드에 특정 파드만 배치시키고 다른 파드는 배제하도록 설정 가능**

<br>

## Node Affinity

노드의 선택을 조금 더 구체적으로 지정하는 기능

nodeSelector는 무조건 and 조건으로 묶이는 반면 요거는 구체적으로 조건 설정 가능

- `requiredDuringSchedulingIgnoredDuringExecution`
  - 해당 affinity 설정에 의해 스케줄링 해줘
  - 무조건 설정된 조건이 있는 노드에만 배치해줘
- `preferredDuringSchedulingIgnoredDuringExecution`
  - affinity 별로 중요도를 지정할 수 있다. (weight)
  - 해당 affinity에 만족하는 node를 못찾더라도 다른 노드에 배치가능

`IgnoredDuringExecution`: 이미 노드에 실행 중인 파드에 대해서는 affinity 영향을 받지 않는다.

`DuringScheduling`: 새로 파드가 생성되어 스케줄링 될 때에 affinity를 어떻게 설정할 것인지 지정

> 결국 affinity는 스케줄링 단계에만 영향을 주는 설정

```yaml
spec:
    affinity:
        nodeAffinity:
            requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                    - key: hw-type
                      operator: In
                      values:
                      - gpu
```
```yaml
preferredDuringSchedulingIgnoredDuringExecution:
    weight: 10
    nodeSelectorTerms:
    # ...
```

- `nodeSelectorTerms ... matchExpressions`는 **or 조건으로 묶이게 된다.**  
여러 matchExpressions 중에 하나라도 만족한다면 배치된다고 보면 된다.
- `matchExpressions 안의 리스트`로 묶인 조건들은 **and 조건으로 묶이게 된다.** (selector 같은 역할 수행)
- `operator`: In, Exists, NotIn, NotExists, Gt, Lt 여러 조건식이 있다.  
    - operator에 해당하는 values에 대해서 조건식을 설정해준다.
    - Exists, NotExists는 해당 key가 존재하는지 여부만 체크(values 노상관)
    - Gt, Lt는 숫자에 대해 크고 작음을 설정
    - In, NotIn 이걸 사용하면 taint, nodeSelector를 같이사용하는 케이스를 이거 하나로 커버 가능 (label 하나만으로 설정가능, 관리 차원에서 좋음)

<br>

## Pod Affinity

파드가 다른 파드를 고려해 노드를 선택

`podAffinity`, `podAntiAffinity` 둘 다 가능  
AntiAffinity는 파드가 배치될 때 그 아래 설정된 affinity 조건 피했으면 좋겠다는 설정

nodeAntiAffinity는 없음

```yaml
spec:
    affinity:
        podAntiAffinity:
            preferredDuringSchedulingIgnoredDuringExecution:
            - weight: 100 # 1 ~ 100
              podAffinityTerm:
                labelSelector:
                    matchExpressions:
                    - key: type
                      operator: In
                      values:
                      - frontend
                topologyKey: "kubernetes.io/hostname"
```
pod 중에 type이 frontend라는 label을 가진 파드가 있다면 해당 파드가 더 많이 존재하는 노드가 더 비선호하는 대상이 된다.  

파드를 대상하기 때문에 조건에 해당하는 파드 개수에 따라서도 결정이 된다.  

`topologyKey`: affinity 조건에 해당하는 어떤 것을 피하고싶다, 혹은 배치되고 싶다에 대한 기준 제시
- `kubernetes.io/hostname`: node의 hostname을 대상으로 제외 혹은 배치 기준(region name 기준도 가능)
- ex) type=frontend라는 label을 가진 파드를 많이 가진 노드의 호스트네임을 기준으로 배치 여부 결정
- cloud service에 특화된 기준도 존재

<br>

## Pod Disruption Budget

자발적인 종료 상황에서도 **유지할 파드 수를 지정**  

- 자발적인 종료
  - 이유가 있어서 파드를 종료시켜야 하는 상황
  - ex) 배포상황에서 이전 버전의 파드를 종료시켜야하는 경우, 어떤 노드를 유지보수하는 상황에서 파드를 비워야 하는 상황

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
    name: my-pdb
spec:
    minAvailable: 2 # 혹은 maxAvailable, 개수 or 퍼센테이지로 설정
    selector:
        matchLabels:
            app: my-app
```
위와 같은 스펙에 대해서  

```
Node-01: pod 2 << drain (모든 파드 비워주는 명령)
Node-02: pod 1
Node-03: pod 0
```
Node-01이 drain 되면 최소 유지 파드 개수가 1이 되어버리므로 쿠베는 Node-01에 파드를 하나 제거하고 비어져있는 Node-03에 먼저 똑같은 pod를 띄우고나서 Node-01 안의 나머지 파드도 비워줌

```yaml
minAvailable: 100%
```
만약 위와 같이 타이트하게 해버리면 파드는 자발적인 종료가 불가능해지는 상황이 되어 버릴 수 있다. drain 작업 자체를 진행할 수 없게 되어버림
