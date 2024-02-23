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

- control plane
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