# ARP protocol

통신하기 전  반드시 필요한 프로토콜

## ARP 프로토콜 하는 일

같은 네트워크 대역에서 통신을 하기 위해 필요한 MAC 주소를, IP주소를 이용해서 알아오는 프로토콜  
같은 네트워크 대역에서 통신을 하더라도 데이터를 보내기 위해서 7계층부터 캡슐화를 통해 데이터를 전송  
(결국 ip 주소, mac 주소 둘 다 필욜함)

### 프로토콜 구조

<img width="899" alt="arp_protocol" src="https://user-images.githubusercontent.com/41675375/206892320-c322aa4e-3635-408e-90c1-8dae78478144.png">

- Source Hardware Address: 출발지 맥주소(6 byte)
- Source Protocol Address: 출발지 ip 주소(4 byte)
- Destination Hardware Address: 도착지 맥주소(6 byte)
- Destination Protocol Address: 도착지 ip 주소(4 byte)
- Hardware Type
  - 2계층에서 사용하는 프로토콜의 타입
  - 2계층에서는 이더넷 프로토콜을 가장 많이 볼 것이다.(`0x0001`)
- Protocol Type
  - 3계층에서 사용하는 프로토콜의 타입
  - 보통 IPv4 밖에 없다.(`0x0800`) 
- Hardware Adderss Length: 맥주소의 길이 (6 byte -> `0x06`)
- Protocol Address Length: IP 주소의 길이 (4 byte -> `0x04`)
- Opcode
  - 어떻게 동작하는지 나타내는 코드
  - 요청(`0x0001`), 응답(`0x0002`)

## ARP 프로토콜 통신 과정

1. ARP 프로토콜 요청을 보내기 위해 헤더내용을 작성한다.

```text
Eth | ARP
```