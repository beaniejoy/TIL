# 3계층과 IP 주소

3계층과 IP 주소에 대한 내용 정리

<br>

## :pushpin: 3계층의 기능

- 네트워크 계층  
- 다른 네트워크 대역(서로 다른 LAN 네트워크 대역)을 연결해주고 데이터 교환 및 제어하는 역할 수행
- 대표적인 3계층 장비: 라우터(Router)
- 멀리있는 곳과 데이터 통신을 하기 위해서는 맥주소 뿐만 아니라 ip address도 있어야 한다.

### 3계층에서 사용되는 주소
- IPv4: 현재 PC에 할당된 IP 주소
- subnet mask: IP주소에 대한 네트워크의 대역을 규정
- gateway: 외부와 통신할 때 사용하는 네트워크 출입구 같은 곳

### 3계층 프로토콜 종류

- ARP 프로토콜: IP주소를 이용해 MAC 주소를 알아내는데 사용되는 프로토
- IPv4 프로토콜: WAN에서 통신할 때 사용하는 프로토콜(IPv6로 아직도 못 넘어가고 있음)
- ICMP 프로토콜: 서로 통신되는지 확인할 때 사용하는 프로토콜

<br>

## :pushpin: Classful IP
![classful_ip_address](https://user-images.githubusercontent.com/41675375/206738680-9c3b0dea-9242-401e-b1be-4846f27405d4.jpeg)

- Class A: 0.........
- Class B: 10........
- Class C: 110.......
- Class D: 1110...... (reserved for multi-casting)
- Class E: 1111...... (reserved for experimental and research purposes)

### Class A

Network ID와 HOST ID로 나눈다.  
Network ID는 클래스 안에서 서로 다른 네트워크를 구분하기 위해 사용  
HOST ID는 같은 네트워크 대역안에 여러 컴퓨터들을 서로 구분하기 위해 사용  
```text
> A 클래스 예시  
A 네트워크 대역   0.0.0.0 ~ 127255.255.255
```
- 네트워크 구분 대역: 0 ~ 127 (적다 - 128개)
- host 구분 대역: 0.0.0 ~ 255.255.255
- subnet mask: 255.x.x.x

A 네트워크 같은 경우 구분지을 수 있는 네트워크 개수가 아주 적다.  
대신에 하나의 네트워크 내에서 구분지을 수 있는 컴퓨터 개수가 아주아주 많다.  
(큰 기관에서나 사용)

### Class B

```text
> B 클래스 예시  
B 네트워크 대역   128.0.0.0 ~ 191.255.255.255  
```
- 네트워크 구분 대역: 128.0 ~ 191.255
- host 구분 대역: 0.0 ~ 255.255
- subnet mask: 255.255.x.x

Class C, D, E도 위와같이 계산하면 된다.
> 참고로 서브넷 마스크  
> - Class C: 255.255.255.x  
> - Class D, E: 없음

### Classful IP 체계 단점

```text
A 클래스 대역에서 32대 PC만 사용한다면?  
100.0.0.1 ~ 100.0.0.31
```
- 낭비가 심하다
- [Introduction of Classful IP Addressing(잘 정리된 글)](https://www.geeksforgeeks.org/introduction-of-classful-ip-addressing/)

<br>

## :pushpin: Classless IP

- 위에서 예시로 알 수 있었듯이, Classful IP 주소 체계는 낭비되는 주소가 너무 많았다.
  - `xxx.xxx.xxx.xxx` 여기서 점으로만 구분해서 클래스를 나눴기 때문에 낭비가 발생
- 이를 보완하기 위해 나온 것이 Classless 접근이다.
  - 꼭 점이 있늰 지점에서 자르는 것이 아니라, 중간에 어디에서나 자를 수 있다.
- 여기서 서브넷 마스크가 활용
  - IP 주소에서 어디부터 잘라서 사용했는지 구분지점을 알려주는 용도

### 서브넷 마스크(subnet mask)

classful ip address 체계에서 **네트워크 대역(Network ID)**을 나눠주는데 사용하는 값  
어디까지 네트워크 대역을 구분하는데 사용하고 어디서부터 호스트를 구분하는데 사용하는지 지정  
- 32 bit, 4 bytes로 표현  
- ex. 255.255.255.192 -> xxx.xxx.xxx.11000000  
- 2진수로 표기했을 때 1로 시작, 1과 1사이에는 0이 올 수 없다는 규칙을 가지고 있음

> 하지만 Classless IP 체계에서도 주소 낭비의 문제가 여전히 존재  
> subnet maskt: x.x.x.11000000 에서 구분할 수 있는 PC는 64대 정도  
> 그런데 실제 PC 개수가 30대 밖에 안된다면 나머지 32개 주소는 낭비되는 것  
> IPv6로 이전하고 있지만 아직 많이 사용안되고 있음

<br>

## :pushpin: Public, Private IP Address

- 위의 Classful, Classless IP 체계에서 낭비의 문제를 보완하고자 공인 IP, 사설 IP 도입
- 사설 IP 대역에 있는 컴퓨터가 외부 인터넷과 네트워크 통신할 때 공인 IP로 바꿔서 통신한다.
- 외부 인터넷 네트워크로부터 공유기에 응답을 보낼 때 공유기가 전에 요청보냈던 사설 IP 대역에 있는 컴퓨터로 응답을 전달해준다.
- 사설 IP에서 공인 IP로 바꿔주는 것을 NAT라고 한다.
  - **NAT**: 특정 IP를 다른 IP로 바꿔주는 기술
  - 이것을 이용해서 IP 주소 부족 문제를 해결한 것
  - 여기서 `port forwarding` 개념도 나온다. (나중에 정리)

<br>

## :pushpin: 특수한 IP 주소

- 0.0.0.0(/0)
  - wildcard
  - 나머지 모든 경로라고 생각하면 된다.
- 127.0.0.1 (127.0.0.x0)
  - 내 자신을 나타내는 주소(내 자신 컴퓨터 주소)
- 게이트웨이
  - 외부로 나가기 위해서 거쳐야 하는 일종의 관문 역할

