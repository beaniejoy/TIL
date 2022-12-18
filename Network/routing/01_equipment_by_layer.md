# 계층별 네트워크 장비와 케이블

- Physical Layer (물리계층)
  - Repeater, Hub
- Data Link Layer (데이터 링크 계층)
  - Switch, Bridge
- Network Layer (네트워크 계층)
  - Router, L3 Switch

<br>

## :pushpin: 계층별 장비 소개

### Physical Layer Equipment
- Repeater
  - 전기신호 증폭 장치
  - cable 전송으로 약화된 신호를 초기화, 증폭, 재전송 기능을 수행
  - 증폭시키는 기능만 있기 때문에 상위 계층의 MAC 주소, IP 주소를 이해하지 못한다.
  - 사실 다른 계층의 장비에서도 repeater의 기능을 가지고 있다.
- Hub
  - 무조건 모두에게 신호를 전달
  - repeater와 마찬가지로 전기적 신호를 증폭시키는 기능도 있음
  - LAN 전송거리를 연장시키고 여러 대의 장비를 LAN에 접속할 수 있도록 한다.  
  (멀티 포트 리피터)
  - hub도 repeater와 마찬가지로 상위 계층의 MAC, IP 주소를 모르기 때문에 모두에게 신호를 전달하는 역할만 수행
  - hub는 1차선 도로라고 생각하면 된다.
    - hub에 연결된 장비들이 동시에 통신을 하려고 하면 충돌(collision)이 발생할 수 있음
    - hub에 연결된 장비들은 하나의 `Collision Domain` 안에 있다고 한다.
    - 그래서 많은 장비를 한꺼번에 하나의 hub에 연결할 수 없다.

### Data Link Layer Equipment
- Bridge
  - 같은 네트워크 장비들을 연결하는 장비
  - 전기적 신호 증폭 기능 뿐만 아니라 MAC 주소를 알고 있어서 Frame 전송 포트 결정 가능(hub와 다른점)
  - Frame을 다시 만들어 전송하는 역할도 수행한다.
- Switch
  - Bridge와 유사하게 MAC 주소를 알고 해당 테이블을 보고 특정 MAC 주소 장비에게 frame 전달 가능
  - Mac Address Table을 보고 특정 포트로만 전송하기 때문에 충돌 발생 X
  - **각각의 포트가 하나의 `Collision Domain`에 있다고 말할 수 있다.**  (bridge와 다른점, 각각의 포트가 하나의 차선으로 사용됨)


### Network Layer Equipment
- **Router**(중요)
  - IP 주소 등 Layer 3 header 내용들을 참조해서 목적지와 연결되는 포트로 packet 전송
  - 같은 네트워크 대역을 넘어서 다른 네트워크 대역에 있는 장비와 통신하기 위해 필요한 장비
  - 특정 인터페이스를 통해 수신된 packet을 보고 거기에 담겨진 목적지 IP 주소를 참조해 목적지와 연결된 인터페이스를 통해 전송할 것을 결정한다.  
  (이를 `Routing`이라고 한다. `Routing Table`을 통해 결정)
  - Router의 기본 기능은 **최적의 경로 결정, 경로에 따른 packet 전송**   
  (그 외 ACL 같은 보안 관련 추가적인 기능도 있음)

> Layer 3 장비들은 멀티캐스트, 유니캐스트, 브로드캐스트를 수신하는 경우 이를 모두 차단한다.

<br>

## :pushpin: 여러가지 Cable

이름에 특정한 정보가 있는 **통신 케이블**

```text
100BASE-T
10BASE 2
10BASE 5
```
- `100`: LAN SPEED (단위: Mbps)
- `BASE`: 통신 방식
  - BASE: Baseband 용 cable(디지털 방식)
  - BROAD: Broadband 용 cable(아날로그 방식)
- `T`: 케이블 종류
  - `T`: `Twisted pair cable`
  - 숫자: 최대 통신 거리

### 케이블 종류
- Twisted Pair
  - Unshielded(UTP)
    - 대부분의 LAN 선이 해당
    - 설치가 쉽고, 가격이 저렴, 하지만 외부 간섭에 취약하고 전송거리가 상대적으로 짧다.
  - Shielded(STP)
    - UTP보다 피복이 더 감싸져있음
    - 가격은 비싸고, 설치가 어렵지만 외부 간섭에 덜 영향을 받는다.
- Coaxial
  - 동축 케이블
  - 위의 TP 계열의 케이블보다 전송거리가 길다.
  - 요즘은 보기가 힘들다.
- Fiber-Optic
  - 광케이블
  - 요즘 많이 사용하는 케이블
  - 빛을 통해 데이터를 전송하는 케이블(유리섬유 같은 것으로 감싸져있다.)
  - 신호 감쇠에 대한 영향이 없다. 하지만 비싸다.

### UTP 케이블

- 명칭: 10/100BASE-T
- connector: RJ-45
- 종류(뭐가 있는지만 기억하자)
  - straight-through cable(쉽게 생각해서 다른 네트워크 장비와 연결)
  - crossover cable(쉽게 생각해서 같은 네트워크 장비끼리 연결)
  - rollover cable