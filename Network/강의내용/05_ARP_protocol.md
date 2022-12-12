# ARP protocol

통신하기 전  반드시 필요한 프로토콜

<br>

## :pushpin: ARP 프로토콜 하는 일

같은 네트워크 대역에서 통신을 하기 위해 필요한 MAC 주소를, IP주소를 이용해서 알아오는 프로토콜  
같 네트워크 대역에서 통신을 하더라도 데이터를 보내기 위해서 7계층부터 캡슐화를 통해 데이터를 전송  
(결국 ip 주소, mac 주소 둘 다 필욜함)

### 프로토콜 구조

<img width="899" alt="arp_protocol" src="https://user-images.githubusercontent.com/41675375/206892320-c322aa4e-3635-408e-90c1-8dae78478144.png">

- Source Hardware Address: 출발지 맥주소(6 byte)
- Source Protocol Address: 출발지 ip 주소(4 byte)
- Destination Hardware Address: 도착지 맥주소(6 byte)
- Destination Protocol Address: 도착지 ip 주소(4 byte)
- Hardware Type
  - 2계층에서 사용하는 프로토콜의 타입
  - 2계층에서는 이더넷 프로토콜을 가장 많이 볼 것이다.(`0x0001`) - **거의 고정**
- Protocol Type
  - 3계층에서 사용하는 프로토콜의 타입
  - 보통 IPv4 밖에 없다.(`0x0800`) - **거의 고정**
- Hardware Adderss Length: 맥주소의 길이 (6 byte -> `0x06`) - **거의 고정**
- Protocol Address Length: IP 주소의 길이 (4 byte -> `0x04`) - **거의 고정**
- Opcode
  - 어떻게 동작하는지 나타내는 코드
  - 요청(`0x0001`), 응답(`0x0002`)

<br>

## :pushpin: ARP 프로토콜 통신 과정

- 유투브 강의 참고해서 ARP 프로토콜 통신 과정에 대해 이해하자
- [ARP 프로토콜 강의](https://youtu.be/LDsp-Xb168E?list=PL0d8NnikouEWcF1jJueLdjRIC4HsUlULi&t=655)

### 주목해야 할 점
ARP 프로토콜, Ethernet 프로토콜 작성할 때 주의할 점이 있다.  
최초로 ARP 요청을 보낼 때,  
ARP 프로토콜의 destination hardware address(상대편 mac 주소)는 `00 00 00 00 00 00`로 설정하고  
Eth 프로토콜의 destination address는 `ff ff ff ff ff ff`로 작성한다.  

2계층 장비인 **스위치**에서 해당 ARP 요청을 받았을 때 2계층 이더넷 프로토콜까지만 열어본다.  
목적지 맥주소가 `ff ff ff ff ff ff`인 것을 확인하고 연결되어 있는 모든 노드에 **브로드캐스팅**을 하게 된다.  
각 노드는 받은 요청 내용을 열어보고 3계층까지 확인했을 때 본인의 ip 주소와 목적지 ip 주소 일치여부를 확인  
만약 불일치하면 그대로 해당 요청 패킷을 버리게 된다.  

그리고 ARP 응답을 받아 원하는 상대의 맥주소를 알게 되면 본인의 ARP 캐시 테이블에 해당 맥주소와 ip 주소를 저장해둔다.  

ARP 통신이 완료된 이후에 원하는 통신이 이루어지게 된다.

### ARP 프로토콜은 같은 네트워크 대역에서만 동작

ARP 프로토콜은 같은 네트워크 대역에서만 움직인다.  
2계층 장비인 스위치에서 ARP 요청을 받아서 브로드캐스팅해줄 때 3계층 장비중 하나인 라우터도 해당 요청을 받게 된다.  
그런데 라우터는 브로드캐스트가 오면 외부 네트워크에 전달해주지 않고 버리게 된다.

<br>

## :pushpin: ARP 테이블 
- 자신과 통신했던 컴퓨터들의 주소는 ARP 테이블에 남는다.
- 영구적으로 저장되어있기 보다 일정시간 지나면 없어지게 된다.
- 그래서 일정시간 지나서 다시 통신하려고 하면 ARP 요청을 해야 한다.
- 만약 수동으로 등록하게 되면 영구적으로 저장할 수 있긴 하다.

```shell
$ arp -an
```
- 본인의 컴퓨터의 ARP 테이블을 확인할 수 있다. (window, linux, mac os 모두 동일)

<br>

## :pushpin: ARP 실습

만약에 이미 arp 테이블에 상대 컴퓨터 맥주소가 등록이 되어 있다면
```shell
$ sudo arp -d [ip address]
```
이렇게 하면 arp 캐시 테이블에서 지울 수 있다.  

### ethernet padding
ARP 통신할 때 ethernet 프로토콜에서 뒤에 footer로 padding이 붙는 경우가 있다.  
- eth: 14 byte, arp: 28 byte
- 프레임 최소 단위인 60 byte를 맞추기 위해 18 byte의 값을 아무 값(0)으로 뒤에 채워넣는다.
- wireshark 캡처 시점에 따라 padding 없이 42 byte로 나오는 경우도 있다.
  - 실제 1계층에서 전기적 신호로 갈 때 뒤에 padding이 붙는다.

> 보통 프레임 최소단위는 60 byte고 최대 단위는 다양하지만 보통 1514 byte
