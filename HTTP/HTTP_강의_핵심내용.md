# HTTP 강의 핵심 내용 정리

- [모든 개발자를 위한 HTTP 웹 기본 지식 - 김영한님](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)

## :pushpin: 인터넷 네트워크

### IP (인터넷 프로토콜)
- **IP address**를 통한 데이터 전달
- **패킷(Packet)** 통신 단위로 데이터 전달
- 한계
  - 비연결성: 패킷을 받을 대상이 없는 대상이거나 불능 상태여도 패킷 전송
  - 비신뢰성: 패킷 분실, 패킷 도착 순서의 문제
  - 프로그램 구분: 동일 IP 내 통신하는 애플리케이션이 다수 존재할 때

### TCP, UDP
- TCP
  - IP 패킷의 한계를 보완
    - 연결성: TCP 3 way handshake를 통해 대상과 연결 확보
    - 신뢰성: 데이터 전송과 전송성공을 sender와 receiver 간에 서로 교환  
    (분실되거나 순서가 이상하면 다시 요청)
    - 프로그램 구분: TCP 정보에는 port 정보도 있기에 특정 가능
- UDP
  - 위의 기능을 지원하지 않음 (IP와 거의 유사)
  - port, 체크섬 정도 추가
  - 최적화에 용이함: TCP는 정해진 규격이 있어 최적화 불가

### PORT
- 특정 노드 내에 돌아가고 있는 프로그램을 구분해주는 역할
- 0 ~ 65535 할당  
(0 ~ 1023: well-known port)

### DNS
- IP 기억하기 쉬운 형태로 제공
- IP 변경될 수 있는 상황에서도 같은 URL 제공

<br>

## :pushpin: URI, 웹 브라우저 요청 흐름

### URI
- `URI` (Uniform Resource Indentifier)
  - Locator(위치, `URL`), Name(이름, `URN`)으로 분류됨
- 주로 URL(유동적)을 사용, URN(고정, ex. isbn)은 있다 정도만
- [URI 스펙 링크](https://www.ietf.org/rfc/rfc3986.txt)

#### URL
> scheme://[userinfo@]host[:port][/path][?query][#fragment]
- scheme
  - 프로토콜에 대한 내용(http - 80, https - 443)
- host
  - ip address, domain address
- path
  - 리소스 직접 경로(`/home/file1.jpg`), 계층적 구조(`/members/100`)
- query
  - query string(parameter)

### 웹 브라주어 요청 흐름
1. 웹 브라우저가 DNS서버에 도메인 주소에 대한 IP 주소를 요청
2. IP주소와 port 정보를 기반으로 HTTP message 생성
3. Socket 라이브러리를 통한 서버 연결(TCP 3-way handshaking) 및 하위 계층으로 메시지 전달
4. TCP/IP 패킷 생성
5. LAN 및 WAN을 통해 서버로 전달
6. 서버는 수신한 TCP/IP 패킷을 해석해 클라이언트가 원하는 데이터를 가져와 응답데이터 생성 및 같은 방식으로 TCP/IP 패킷 생성해서 클라이언트에 전달
   - Content-Type, Content-Length, body 데이터 등
7. 클라이언트는 응답데이터를 수신해 해당 body 데이터를 화면에 랜더링

<br>

## :pushpin: HTTP 기본

### 모든 것이 HTTP
- HTTP 메시지
  - HTML, TEXT, XML, JSON, IMAGE, 음성, 영상, 거의 모든 형태 리소스를 주고 받을 수 있음
- HTTP 버전
  - HTTP/0.9: GET만 지원, 헤더 X
  - HTTP/1.0: 메서드, 헤더 추가
  - HTTP/1.1: 현재 대다수 사용되고 있는 버전, keep-alive 기능 default 제공
  - HTTP/2, HTTP/3(UDP): 성능 개선에 초점
- 특징
  - 비연결성, 무상태 프로토콜(stateless), 클라이언트 서버구조, HTTP 메시지 사용

### 클라이언트 서버 구조
- 하나의 노드에 클라이언트, 서버라는 개념이 불명확, 현재는 역할이 명확
- 서버는 서버의 역할에만 충실하면 됨(REST API 제공용)

### stateful vs stateless
- stateful
  - 중간에 점원이 바뀌면 안됨(한 점원이 고객의 상태정보를 알고 있음)
- stateless
  - 중간에 점원이 바뀌어도 됨(고객이 다른 점원에게 기존의 정보들을 다같이 요청하면 됨)
  - 유동적인 서버 증설 가능
  - 한계도 분명 존재: 로그인 같은 상태 정보를 저장해야되는 경우

### 비연결성(connectionless)
- 연결성
  - 단점은 유휴상태여도 해당 클라이언트와 연결을 유지해야함
- 비연결성
  - 서버자원을 효율적으로 사용 가능  
  (연결을 유지하지 않기때문에 여러 사용자 request를 동시에 처리 가능)
  - HTTP 기본 모델
  - 3-way handshaking 시간 비용 추가
  - 하나의 파일만 요청하는 것이 아니라 수많은 파일들이 같이 요청됨(비효율적)
  - HTTP는 현재 `Persistent Connections`를 기본으로 사용  
  (이전에는 `keep-alive` 상태를 부여했어야 했는데 지금은 default로 설정됨)

### HTTP 메시지
- format
  - start-line
    - 요청메시지: `GET /path HTTP/1.1`
    - 응답메시지: `HTTP/1.1 200 OK`
  - header
    - 요청메시지: `Host: www.google.com`
    - 응답메시지:  
    `Content-Type: text/html;charset=UTF-8`  
    `Content-Length: 3423`
  - empty line (`CRLF`)
  - message body
    - 전달할 메시지 (JSON, HTML, etc..., byte로 표현할 수 있는 모든 데이터 가능)
- **단순함, 확장 가능**

<br>

## :pushpin: HTTP Method

### HTTP API를 만들어보기
- **리소스 식별**이 중요 (리소스 자체가 중요 ex. 회원 `/members`)
- 리소스와 행위를 분리
  - 리소스: 회원
  - 행위: 조회, 등록, 삭제, 변경 (CRUD)
- 메소드(행위) 종류
  - `GET`: 리소스 조회
  - `POST`: 요청 데이터 처리, 주로 등록에 사용
  - `PUT`: **리소스를 대체**, 해당 리소스가 없으면 생성
  - `PATCH`: 리소스 부분 변경
  - `DELETE`: 리소스 삭제
- 기타 메소드
  - `HEAD`: 상태줄과 헤더만 반환
  - `OPTIONS`: 대상 리소스에 대한 사용가능 메소드 설명(CORS에서 주로 사용)
  - `CONNECT`: 대상 자원으로 식별되는 서버에 대한 터널 설정(**거의 사용 X**)
  - `TRACE`: 대상 리소스에 대한 경로를 따라 메시지 루프백 테스트 수행(**거의 사용 X**)

### GET, POST
- GET
  - query를 통해서 데이터를 전달
- POST
  - 메시지 바디를 통해 서버로 요청 데이터 전달(데이터를 보낼테니 이 데이터를 처리해줘~)
  - 신규 리소스 등록, 프로세스 처리에 사용, 다른 메소드로 처리하기 애매한 경우

### PUT, PATCH, DELETE
- PUT
  - 리소스 대체: 있으면 대체, 없으면 생성(overwrite)
  - 클라이언트가 리소스 식별 (/members/100)
- PATCH
  - 리소스 부분 변경
- DELETE
  - 리소스 제거

### HTTP 메서드의 속성
- 안전
  - **호출해도 리소스를 변경하지 않는다.** (ex. GET)
  - **리소스 자체만 고려**, 다른 내용은 고려 X (로그로 인한 서버장애 부분까지?? NO!)
- 멱등성
  - **1번, 100번 호출하든 결과가 똑같은 성질**
  - GET, PUT, DELETE는 멱등 O / POST는 멱등 X
  - 어디에 사용? -> **자동복구 메커니즘**  
  (서버가 Timeout등으로 정상작동하지 않을 때 클라이언트가 같은 요청을 할 수 있는가의 판단근거가 된다.)
  - 멱등은 외부요인으로 중간에 리소스가 변경되는 것까지 고려X  
  (여러 사용자 요청에 의한 동시성 이슈로 Repeatable Read가 깨진 경우까지 고려하지 않음)
- 캐시가능
  - 응답결과 리소스를 캐시해서 사용가능한가
  - GET, HEAD, POST, PATCH 캐시가능 -> **GET, HEAD 정도만 사용**

<br>

## :pushpin: HTTP 메서드 활용

### 클라이언트에서 서버로 데이터 전송
- 데이터 전달 방식 2가지
  - query parameter
    - GET,
    - 정렬 필터/검색어
  - message body 
    - POST, PUT, PATCH
    - 회원가입, 상품주문, 리소스 등록/변경
- 4가지 상황
  1. 정적 데이터 조회 (static)
  2. 동적 데이터 조회 (query parameter)
  3. HTML form 데이터 전송 (parameter)
     - POST 전송: `application/x-www-form-urlencoded`
     - GET 전송: query parameter
     - multipart/form-data
       - binary 데이터 전송시, 
       - 메시지 바디에 form 내용과 다른 종류의 여러 파일을 다같이 전송하기 때문에 multipart
  4. HTTP API: 
     - 서버 to 서버
     - 웹/앱 클라이언트 (ajax, axios)
     - POST, PUT, PATCH > `application/json` (**사실상 표준**)
     - GET > query parameter

