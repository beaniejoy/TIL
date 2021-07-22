# Load Balancing

- Hashing 알고리즘 및 여러 알고리즘에 대해 알아보기
- Session Clustering 찾아보기

## 📌 LB Algorithms

### Round Robin

- 기본적인 LB 알고리즘
- 요청이 들어오는 대로 서버마다 균등하게 요청을 분배합니다. 가장 단순한 분배 방식입니다.

### Weighted Round Robin

- Round Robin방식으로 분배하지만 서버의 가중치에 따라 요청을 더 분배하기도, 덜 분배하기도 합니다.
- 서버 가중치는 사용자가 지정할 수 있고 동적으로 조정되기도 합니다.

### Hashing Algorithms

- (작성중)

### Least Connection

- 서버마다 연결된 커넥션이 몇개인지 체크하여 커넥션이 가장 적은 서버로 요청을 분배하는 방식입니다.

### Weighted Least Connection

- Least Connection방식으로 분배하지만 서버 가중치에 따라 요청을 더 분배하기도, 덜 분배하기도 합니다.
- 서버 가중치는 사용자가 지정할 수 있고 동적으로 조정되기도 합니다.
- 서버 풀에 존재하는 서버들의 사양이 일관적이지 않고 다양한 경우 이 방법이 효과적입니다.

## 📌 세션 불일치

서버 확장(scale-out)을 진행하면 세션이 각 인스턴스마다 고유한 공간으로 생성되기에 공유 X  
`A instance`에서 로그인했는데 `B instance`에서는 인증완료가 안된 상태로 남을 수 있음  
이를 동기화해주는 것이 중요

### Session Storage

- 각 서버가 공통된 저장소를 바라보고 접근
- 개별 서버를 따로 구성해야 한다.
- `SPoF` 이슈 발생 가능
- Redis - Replication, Clustering으로 해결 가능

> SPoF
> - Single Point of Failure
> - 시스템 구성요소 중에 하나라도 작동하지 않으면 전체 시스템이 중단되어 버리는 지점, 요소

### Sticky Session

- 첫 request에 대한 response를 내준 서버에 계속 붙어있는 것
- 특정 세션의 요청을 처음 처리한 서버로만 보내는 것
- 로드밸런서만 설정해주면 된다.

**단점**
- 로드밸런싱이 잘 동작하지 않을 수 있다
- 특정 서버만 과부하가 올 수 있다.
- 특정 서버 Fail시 해당 서버에 붙어 있는 세션들이 소실될 수 있다.

### Session Clustering

- 여러 WAS의 세션을 동일한 세션으로 관리하는 것