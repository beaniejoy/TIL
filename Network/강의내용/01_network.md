# 네트워크 기초

## Table of Contents

- [네트워크 기초](#네트워크-기초)
  - [Table of Contents](#table-of-contents)
  - [:pushpin: 네트워크 무엇인가](#pushpin-네트워크-무엇인가)
  - [:pushpin: 인터넷?](#pushpin-인터넷)
  - [:pushpin: 네트워크 분류](#pushpin-네트워크-분류)
    - [크기에 따른 분류](#크기에-따른-분류)
      - [LAN, WAN](#lan-wan)
    - [연결 형태에 따른 분류](#연결-형태에-따른-분류)
  - [:pushpin: 네트워크 통신 방식](#pushpin-네트워크-통신-방식)
  - [:pushpin: 네트워크 프로토콜](#pushpin-네트워크-프로토콜)
    - [여러 프로토콜](#여러-프로토콜)
    - [패킷](#패킷)
  - [:pushpin: traceroute](#pushpin-traceroute)

<br>

## :pushpin: 네트워크 무엇인가

- 노드들이 데이터를 공유할 수 있게 하는 디지털 전기 통신망의 하나  
- 분산되어 있는 컴퓨터(노드, 호스트)를 통신망으로 연결한 것  

<br>

## :pushpin: 인터넷?

- 문서, 그림 영상과 같은 여러가지 데이터를 공유하도록 구성된 세상에서 가장 큰 전세계를 연결하는 네트워크
- 흔히 `www`를 인터넷으로 착각, `www`는 인터넷을 통해 웹과 관련된 데이터를 공유
- 네트워크 > 인터넷 (**인터넷은 전세계에서 가장 큰 네트워크 통신망**)
- 인터넷을 이용해서 가장 많이 사용하는 서비스가 **웹서비스**(www)

<br>

## :pushpin: 네트워크 분류

### 크기에 따른 분류
- **LAN(Local Area Network)**
- **WAN(Wide Area Network)**
- MAN(Metropolitan Area Network)
- 기타(VLAN, CAN, PAN 등)

#### LAN, WAN
- LAN, WAN을 구분할 수 있는 것 중에 장비로 판단 가능
- LAN
  - 하나의 장비로 여러 컴퓨터가 연결되어 있으면 하나의 LAN 안에 연결된 상태
- WAN
  - 여러 개의 LAN을 서로 연결시켜주는 망


### 연결 형태에 따른 분류
- Star형
  - 중앙 장비에 모든 모드가 연결
  - 중앙장비(공유기 같은)가 고장난다면 네트워크 통신 불가
- Mesh형
  - 여러 노드들이 서로 그물처럼 연결
  - 어느 하나 노드에서 이상이 있어도 다른 노드들을 통해 연결 상태 유지 가능
  - ex. [Submarine Cable Map](https://www.submarinecablemap.com/)
- Tree형
  - 나무 가지처럼 계층 구조로 연결
- 기타(링형, 버스형, 혼합형)
- 실제 인터넷은 혼합형(여러 형태 혼재되어 있는 형태)

<br>

## :pushpin: 네트워크 통신 방식

- 유니 캐스트
  - 같은 네트워크 대역에서의 특정 대상과 1:1 통신
- 멀티 캐스트
  - 같은 네트워크 대역에서의 특정한 다수와 1:N 통신
- 브로드 캐스트
  - 같은 네트워크 대역에 있는 모든 대상과 통신

<br>

## :pushpin: 네트워크 프로토콜
- 네트워크에서 데이터는 어떻게 주고 받는지
- 네트워크에 있는 특정한 사용자를 어떻게 찾아낼 것인지(여기서 프로토콜 필요)
- 프로토콜은 일종의 약속, 양식
  - 택배를 보내기 위해 택배 양식을 정하듯

### 여러 프로토콜
- 상황별로 여러 통신 양식(프로토콜)이 존재
- **Ethernet protocol**(MAC address)
  - 가까운 곳과 연결할 때
- **ICMP, IPv4, ARP**(IP address)
  - 멀리 있는 곳과 연결할 때
- **TCP, UDP**(port number)
  - 여러 가지 프로그램으로 연결할 때

### 패킷
- 여러 프로토콜들로 캡슐화된 내용
```text
Ethernet | IPv4 | TCP | data
```

<br>

## :pushpin: traceroute

```shell
$ traceroute 8.8.8.8
 1 
 ...
 ...
 ...
 9  * * *
10  dns.google (8.8.8.8)  34.268 ms  32.200 ms  31.895 ms
```
google DNS 서버로 가는 라우트 경로 표시  
1번에 나오는 주소는 우리집 router 주소(공유기)