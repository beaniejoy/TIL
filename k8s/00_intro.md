# Kubernetes 배경

- Container Orchestration

<br>

## 등장

도커 대중화가 되면서 k8s도 부각됨

- docker 이전에 구글에서 내부적으로 Borg(보그)라는 orchestration tool을 개발해 사용
- docker 기반 컨테이너 기술이 성숙해지면서 구글이 Borg를 리눅스 재단에 기부 -> 이 때 kubernetes라는 이름이 붙여짐
- 리눅스 재단은 하위에 Cloud Native Computing Foundation(CNCF)을 만들고 Kubernetes를 첫 번째 프로젝트로 호스팅 시작
- 현재 대세(킹왕짱)

<br>

## Concept

### k8s는 운영체제 레벨의 가상화 기술
- Virtual Machine은 하드웨어 레벨의 가상화 기술
- 파일시스템의 격리
  - `/volume/process-1`, `/volume/process-2`은 Host에서는 두 개의 디렉토리로 보이지만 각 프로세스별로 하나씩 할당해 각각의 디렉토리에만 접근 가능하게 하면 서로 바라볼 수 없는 격리된 환경 구성 가능
  - 각각의 프로세스는 각 디렉토리를 root로 사용
- 네트워크 포트 매핑
  - process-1 > 8080 ... 30080(host)
  - process-2 > 8080 ... 30090(host)
- PID도 서로 공유 X
- 자원도 각각 할당받은 범위 내에서만 host 자원 사용가능(Control Group, cgroup)
- 하나의 프로세스가 컨테이너로 동작하게 됨
- 위의 운영체제 레벨의 가상화 기술을 지원해주는 소프트웨어가 docker

### Docker

- Docker Containerization (dockerize)  
  - 내가 만든 애플리케이션을 이미지화해서 컨테이너로 실행해서 사용 가능
  - 도커 이미지로 만들어지면 어떤 환경에서도 docker 환경만 구성되면 실행가능
  - 이부분은 개발환경과 실환경의 격차를 완전 줄여줌
- docker를 왜써?
  - 사실 그냥 배포하는 것과 별다를 것이 없어보임
  - MSA에서 오히려 취약해 보임(docker container의 격리성으로)
  - 애플리케이션 간에 통신도 필요한데 격리로 인해 이러한 것들이 까다로워짐
  - 물론 docker compose가 나와서 하나의 네트워크로 묶어주는 설정도 생김
- docker 컨테이너 활용한 장점이 존재
  - 컨테이너 실행과 종료가 간편해짐(할당과 회수가 바로바로 이루어짐)
  - 호스트를 여러 개 구성해서 각각에 컨테이너로 추가와 삭제가 간편
  - 여기서 추가로 여러 호스트에 분산된 컨테이너를 하나로 묶을 수 있는 네트워크 관리와 리소스 관리 등을 해줄 수 있는 docker container 단계보다 더 고차원적인 개념이 필요하게 됨
  - 그래서 등장하게 된 것이 **컨테이너 오케스트레이션**
    - docker: 컨테이너 런타임 역할을 수행
    - container orchestration: 모든 container들의 network, resources 등 관리 및 조율(>> kubernetes)

### kubernetes

- 유연성
  - 하나의 컨테이너에 문제 발생시 제거하고 새로 하나 다시 띄워줌
  - 스케일을 2개에서 3개로 유연하게 조정가능

### Control Plane, Node

`Node`: 이전에는 worker node, master node 이렇게 불렀는데 보통 worker node를 지금은 통칭해서 Node라고 부르고 있다.
`Control Plane`: 여러 Node들과 그 안에 들어갈 container들의 상태를 관리하고 사용자 측면에서 명령어를 받아 수행해주는 역할을 담당(과거에 master node로 불림)

- `control plane`
  - `API Server`: 사용자들에게 명령을 받아서 Node와 통신하고 필요한 작업을 수행
    - 보통 cli 도구(`kubectl`..)들을 이용해 사용, curl로 호출할 수 있지만 잘 사용 X
  - etcd: 쿠베 클러스터, 컨테이너 관련된 모든 데이터를 저장하는 데이터 저장소 역할(메모리, 파일 저장소)
  - `Scheduler`
    - 컨테이너가 생성될 때 적합한 노드를 선택해주는 역할
    - 어떤 노드에 실행을 해줄지 결정해줌, 어떤 알고리즘에 의해 노드를 선택하는지 복잡함
    - 노드나 스케줄러의 동작방식에 대해 깊게 알지는 못해도 되지만 특정 노드에만 적용되게끔 하려면 일종의 커스터마이징이 필요
  - `Controller Manager`: 쿠베의 다양한 컨트롤러를 관리, API Server가 해야할 일을 알려주는 역할, (Node, Job Controller 등), controller - api server 간에 이어주는 역할도 담당
    - ex. node controller는 node 하나에 이상 발생시 api server를 통해 etcd에 해당 노드의 상태값을 변경시켜준다.
  - `Cloud Controller Manager`: 클라우드 서비스의 다양한 API를 쿠베와 통합시켜주는 역할
- `node`
  - `kubelet`: 컨테이너 실행, 종료, 및 상태 모니터링 등을 수행
    - 자체적으로 수행된다기보다 api-server의 요청을 받아서 수행
  - `kube-proxy`: container network를 관리

<br>

## Reconciliation, Self-Healing

- Imperative System(명령형 방식)
  - 명령을 통해 바로 원하는 작업을 수행하도록 지시하는 방식
  - ex) $ docker run mysql >> 바로 mysql container를 생성해줌
  - 절대적인 명령을 주는 것. 현재 컨테이너 상태나 개수에 상관없이 2개를 추가 생성
- **Declarative System(선언형 방식) > kube 방식**
  - 프로그램에 명령을 하는 것이 아니라 목표로 하는 상태를 제시하는 방식
  - 그 상태에 도달하기 위한 방법은 프로그램에 위임
  - ex) container 3개가 있어야 한다.
  - 지금 컨테이너 상태를 2개로 맞춰야 한다. 기존 1개면 1개를 추가, 기존 3개면 1개를 제거 

쿠베는 선언형 방식이기 때문에 kube에 어떠한 명령을 전달해도 current state에 바로 도달하는 것이 아님  
즉 `Desired State`, `Current State` 두 개의 상태가 존재하고 Current > Desired를 모니터링하고 불일치 발생시 조정(**Reconciliation**)하는 과정을 거친다.

kube가 loop를 돌면서 불일치 상태인지 체크하게 된다. (비효율적이지 않나?) 이게 쿠베가 추구하는 방식인 듯  

> **Reconciliation Pattern**이라고 한다.

Self-healing: 쿠베의 선언적 특징에서 자연스럽게 나오는 기능  
컨테이너의 desired state에 벗어난다면 알아서 자동 조정
(운영환경에서 강력한 기능)

```
current state > container:1.0.0
desired state > container:1.0.1 (이미지 push가 안되어있는 상태)
```
위 경우에 불일치 상태가 발생하게 되어 계속해서 조정 작업을 진행하게 될 텐데 무한 루프로 계속 retry 하게 될 것이다. (1.0.1 버전 이미지 푸시하지 않거나 desired state를 다시 1.0.0으로 되돌리지 않는 이상)

컨테이너의 재시작(restart) 명령은 조금 애매(선언적 특징 때문)  
쿠베에서 명령형 커맨드를 지원, 이를 이용해 재시작 가능
(혹은 컨테이너 수량을 0으로 했다가 다시 늘려주는 방식도 있음)

> 결국 핵심은 원하는 컨테이너 스펙을 쿠베에 전달하는 것임. 이 스펙에 대한 명세서에 대해서 공부해야 함

### 애플리케이션에게 쿠베란

- **Declarative**: 모든 동작이 선언적으로 이루어짐
  - 코드 작성(명세서 작성)을 정적으로 관리 가능, 선언만 해두면 쿠베가 알아서 자동 조정을 해주기 때문
- **Idempotency**: 같은 정의를 여러 번 적용해도 항상 같은 결과가 나옴(멱등성)
- **Self-Healing**: 예기치 못한 불일치가 발생해도 스스로 복구
- **Consistency**: 쿠베 현재상태나 동작과 관계없이 최종 스펙(마지막에 정의한 스펙)에 상태를 맞춘다.

쿠베가 관측하기 좋은 애플리케이션 만드는 것이 중요  
목표로 하는 상태 잘 정의하기(부족하지도 않고 과하지도 않게 정의하는 것이 중요)  
체계적인 버전 관리 혹은 자동화 된 빌드와 배포  
갑자기 종료되거나 새로 실행되어도 동작에 문제가 없는 애플리케이션

<br>

## Selector와 Label

여러 컨테이너가 있는 쿠베 환경에 대해서 외부에서 호출한 경우 어떻게 내부 컨테이너를 구분할 것인가  
(정확히 특정 컨테이너를 호출하는 것이 목표가 아니라 여러 개 컨테이너중 아무거나 하나만 호출하면 된다 느낌)

nginx를 이용해 분기처리하면 되지 않을까? > 쿠베에서 컨테이너를 띄울 때 nginx 웹서버용 컨테이너를 따로 띄우는 경우는 없음  
핟당 받은 컨테이너 ip로 구분하면 되지 않을까? > 쿠베 환경에서 컨테이너는 없어지고 새로 생기는 일이 빈번히 발생, 그 과정에서 ip 재할당 가능

여기서 Label 개념이 나옴 > 일종의 tag 개념으로 구분해준다.  
쿠베는 label을 통해서 spec의 변화를 감지해 reconciliation을 수행하기도 한다.

<br>

## Kube의 네트워크

쿠베에서는 물리적 네트워크가 아닌 가상의 네트워크를 사용  

특정 노드에 app container 2개가 돌고 있는데 같은 port를 사용한다면 어떻게 구분할 것인가?  
해당 노드의 임의의 포트를 각각 다르게 포트포워딩하는 방식이 있을 수 있다. 하지만 미봉책에 불과(개발하기에도 까다로움)  
- 노드에 할당되는 컨테이너가 계속 바뀌고 컨테이너 자체도 제거되었다가 새로 생성되는 일이 자주 발생

그래서 쿠베는 overlay network를 제공

```
my-app-network >> my-app 1..5 5개 중에 임의의 한 개에 요청 전달
```
요청 들어올 때는 my-app-network 네트워크 객체를 호출하게 되고 네트워크 객체는 연결되어 있는 container 중 한 개에 요청을 전달하게 된다.  
**쿠베 내부, 외부 요청 모두 overlay network 객체를 통해 전달 가능**

overlay network <-> container(selector)  
selector를 통해 하나의 네트워크로 묶을 수 있음, network 객체도 하나의 애플리케이션 단위로 사용됨  

쿠베 내부에서 다른 컨테이너로 호출해야할 때  
`http://my-app-network/api/check` 이런 식으로 network 이름을 붙여서 호출  

`my-app-network(10.0.21.93) >> my-app 임의의 3개로 할당`  
일종의 라우팅 테이블(**라우팅 규칙 집합체**)이 있는데 그걸 통해서 1개를 선택해서 요청을 보내게 됨(nginx 같이 별도의 애플리케이션 레벨의 프로그램이 돌아가서 요청 할당해주는 것이 아니라 매핑테이블로 동작하는 느낌)  

응답 줄 때도 마찬가지로 실제응답하는 **컨테이너의 ip주소로 응답하는 것이 아닌 네트워크 객체의 ip주소로 응답**하게 됨  

결국 쿠베는 내부에서 추상화된 네트워크 레이어 객체로 요청보내고 응답을 받는 형태  
(container간 직접 연결되어 할 수 있으나 권장되지도 않고 거의 그런식으로 사용 X)

- 외부 통신 

```
외부 >> my-app-inbound-network(외부 전용) >> my-app-network(내부용) >> containers  
```

여기서 inbound-network는 L7단계의 라우팅 프로그램이 돌아가는 형태(nginx 같은)

<br>

## 저장소와 볼륨

container는 제거와 생성이 빈번하게 발생  

```
/vol/container-01 (host) >> (container's root)
```

host의 특정 디렉토리가 container의 root 디렉토리가 되는데 만약 컨테이너 종료되면 호스트의 해당 디렉토리들도 다 사라지게 된다.

> 비정상적으로 종료된 컨테이너 로그를 확인하려면??

추상화한 volume 객체를 쿠베 내부에 따로 둬서 특정 storage하고 연결되도록 해서 컨테이너가 제거되어도 디렉토리는 사라지지 않도록 할 수 있다.  
이렇게 되면 컨테이너가 사라지고 다른 노드에 다시 생성된다 해도 일관성을 유지할 수 있다.

- 임시 볼륨
  - 컨테이너 라이프사이클에 대해서만 유효
  - 지워져도 상관 없는 파일 저장할 때 사용(요청에 대해 저장된 임시파일들)
  - 로그 파일도 임시 볼륨에 하는경우 많음 (fluentd를 통해 따로 로그 수집하면 되기에)
- 영구 볼륨
  - 저장공간이 노드 외부에 위치해서 어떤 경우에도 일관성 있게 참조 가능
  - 미리 할당해둔 저장소를 컨테이너에 연결, 필요한 만큼 프로비져닝(?)
  - 여러 컨테이너가 같은 저장 공간 동시 참조 가능
  - 기본적으로 네트워크 저장소 형태로 동작 >> 성능, 비용 고려해야한다.

<br>

## CRD를 이용한 Kubernetes의 확장과 플러그인

argo, fluentd, helm 등 여러 확장 플러그인들이 존재

- **custom resource definition(CRD)**

사용자가 직접 선언해서 사용할 수 있도록 확장한 kube 객체
ex) cpu 특정 퍼센테이지 이하일 때만 해당 command를 실행시켜주는 객체 스페을 정의가능

- operator

CRD와 쿠베 api를 연결해주는 역할(CRD의 바뀐 스펙을 확인하고 kube api를 이용해 조정)  
ex) kube api를 이용해 cpu 현재 상태를 계속 체크하고 CRD를 실행시켜주는 api를 호출

- **API Aggregation**

별도의 API Server를 만들어서 kube API를 확장

- Custom Resource: 선언적
- API Aggregation: 명령형

또한 다음 개념도 존재

- **CSI(Container Storage Interface)**: 일종의 volume storage를 이용할 수 있도록 해주는 driver 역할
- **CNI(Container Network Interface)**: network 관련 plugin, 보통 전체 쿠베 환경에서 하나의 CNI가 존재
- **Device Plugin**: 하드웨어 플러그인, 일부 노드에만 GPU가 존재한다면 이걸 사용할 수 있도록 해줌, kube에서 지원하지 않는 하드웨어에 대해 인식과 제어가 가능하도록 해주는 plugin, 필요에 의해 가끔 설치해서 사용하는 경우 존재