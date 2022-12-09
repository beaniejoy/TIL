# Ethernet Protocol

2계층인 데이터링크 계층에 대해서 알아보자. (Ethernet Protocol)

## :pushpin: 2계층 데이터링크 계층

하나의 네트워크 대역(같은 네트워크, LAN)에서의 여러 장비들 간 데이터 교환할 때 사용  
오류 제어나 흐름 제어를 수행한다.
- 관련 네트워크 장치: 스위치
다른 네트워크 대역, 즉 다른 LAN 대역에 있는 네트워크와 통신하기 위해서는 상위계층인 3계층의 도움이 필요하다.  

<br>

## :pushpin: 2계층에서 사용하는 주소

- MAC address  
- 컴퓨터에서는 물리적 주소라는 표현으로 알 수 있음
```text
6C-29-95-04-EB-A1
```
- 앞의 3개(`6C-29-95`)는 **OUI**
  - IEEE에서 부여하는 일종의 제조회사 식별 ID
- 뒤의 3개(`04-EB-A1`)는 고유번호
  - 제조사에서 부여한 고유번호

<br>

## :pushpin: 프로토콜 구조

<img width="883" alt="Ethernet_header" src="https://user-images.githubusercontent.com/41675375/206674806-68ce8217-52a5-4f18-b02d-bba2f428cf11.png">

- Destination Address: 목적지 주소
  - 6 bytes로 이루어져 있음
- Source Address: 출발지 주소
  - 6 bytes
- Ethernet Type
  - 2 bytes
  - 프로토콜 타입
    - 페이로드안에 어떤 프로토콜 내용이 있는지 모른다.
    - 상위 프로토콜이 무엇인지 미리 알려주는 용도(IPv4: `0x0800`, ARP: `0x0806`)

```shell
$ ifconfig

en0: ...
  ether aa:aa:aa:aa:aa:aa
  inet 172.xx.xx.x
```
위와 같이 Mac address 알아낼 수 있다.