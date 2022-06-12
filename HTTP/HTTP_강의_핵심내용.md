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

### HTTP 설계 예시
- 미네랄을 캐다 -> `미네랄(리소스)`에 초점을 둬야 함. 캐다에 초점을 맞추면 안된다.
- 캐다라는 행위에 대해서는 Method로 빼야 한다.

#### (HTTP API) POST 기반 등록 - 회원 관리 시스템 예시
- 회원 목록 조회 `GET /members`
- 회원 등록 `POST /members`
- 회원 조회 `GET /members/{id}`
- 회원 수정 `PUT/PATCH/POST /members/{id}`
  - 보통은 `PATCH`(부분 수정)
  - 게시글 수정 같은 삭제 후 다시 저장하는 경우에는 `PUT`
  - 이도저도 아닐 때 `POST`
- 회원 삭제 `DELETE /members/{id}`

#### (HTTP API) PUT 기반 등록 - 파일 관리 시스템
- 파일 목록 조회 `GET /files`
- 파일 조회 `GET /files/{filename}`
- 파일 등록 `PUT /files/{filename}`
- 파일 삭제 `DELETE /files/{filename}`
- 파일 대량 등록 `POST /files`

#### POST, PUT 방식의 차이
- POST 방식으로 등록
  - 클라이언트는 등록될 리소스 URI 모름
  - 서버에서 신규 등록된 리소스의 URI를 만들어서 응답을 해준다.
    - header: `Location: /members/100`
    - body: `{ id: 100, ... }` 
  - **컬랙션(collection)** 개념: 서버가 관리하는 리소스 디렉토리(`/members`)
- PUT 방식으로 등록
  - POST 방식과 다르게 PUT이 리소스 등록으로 사용
  - 클라이언트가 리소스 URI를 알고 있어야 한다. (클라이언트가 file 이름을 알고 등록해야 한다.)
  - **스토어(store)** 개념: 클라이언트가 관리하는 리소스 저장소(`/files`)
  
#### HTML FORM 사용
- FORM 형식은 `GET`, `POST` 방식만 지원
- `/new`, `/edit`, `/delete` 동사로된 리소스 경로 사용
- HTTP Method로 해결하기 애매한 경우 사용
  - 회원 목록 조회: `GET /members`
  - 회원 등록 폼: `GET /members/new`
  - 회원 등록: `POST /members/new` (or `/members`) - 등록 폼 주소와 같이가는 것이 좋다.
  - 회원 상세 조회: `GET /members/{id}`
  - 회원 수정 폼: `GET /members/{id}/edit`
  - 회원 수정: `POST /members/{id}/edit` (or `/members/{id}`) - 수정 폼 주소와 같이가는 것이 좋다.
  - 회원 삭제: `POST /members/{id}/delete`

#### URI 설계 개념
- document
  - 단일 개념, 객체 인스턴스, DB row (`/members/100`, `/files/star.jpg`)
- collection
  - 서버가 관리하는 리소스 디렉토리
- store
  - 클라이언트가 관리하는 리소스 저장소
- controller(control URI)
  - 문서, 컬랙션, 스토어로 해결하기 어려운 추가 프로세스 실행
  - 동사 직접 사용 (`/delete`, `/new`, `/edit`)
- 참고
   - https://restfulapi.net/resource-naming

<br>

## :pushpin: HTTP 상태코드

### HTTP 상태코드
- 종류
  - `1xx`(Informational): 요청이 수신되어 처리중
    - 거의 사용 X
  - `2xx`(Successful): 요청 정상처리
  - `3xx`(Redirection): 요청을 완료하려면 추가행동이 필요
  - `4xx`(Client Error): 클라이언트 오류, 잘못된 문법 등으로 서버가 요청을 수행할 수 없음
  - `5xx`(Server Error): 서버 오류, 서버가 정상 요청을 처리하지 못함
- 모르는 상태코드에 대해서 클라이언트는 상위 상태코드로 해석(`299` -> `2xx` 처리)

### `2xx` Successful(성공)
- `200 OK`: 요청 성공
- `201 Created`: 요청 성공
  - 새로운 리소스가 생성됨(`Location: /members/100`)
- `202 Accepted`: 요청이 접수되었으나 처리가 완료되지 않았음
  - 배치 처리 같은 곳 사용(요청 접수후 수시간 이후에 배치 프로세스가 요청처리)
- `204 No Content`: 요청을 성공적으로 수행했지만, 응답 페이로드 본문에 보낼 데이터가 없음
  - 웹 문서 편집기에서 저장 버튼(응답으로 아무것도 필요 없을 시)
  - 204 메시지만으로 성공 인식할 수 있도록(편집기는 save 누르고 바로 이어서 편집하기에)

### `3xx` Redirection 1
- 요청을 완료하기 위해 유저 에이전트(클라이언트 프로그램, 주로 웹브라우저)의 추가 조치 필요
- `301` ~ `308` 중요
- 리다이렉션 이해
  - 웹브라우저는 `3xx` 응답 결과에 `Location` header를 가지고 해당 위치로 다시 요청
  - 영구 리다이렉션: 특정 리소스의 URI가 영구적으로 이동(**자주 사용 X**)
  - 일시 리다이렉션
    - 일시적인 변경 (주문 완료 후 주문 내역 화면으로 이동) 
    - PRG (POST/REDIRECT/GET)
  - 특수 리다이렉션: 결과 대신 캐시 사용

#### 영구 리다이렉션
- 원래 URL 사용 X, 검색 엔진 등에서도 변경 인지
- `301 Move Permanently`: 리다이렉트시 요청 메서드가 **GET으로 변경**, **본문 제거될 수 있음** (MAY)
- `308 Permanent Redirect`: 301 기능과 같음, **리다이렉트시 요청 메서드와 본문 유지** (거의 사용 X)

### `3xx` Redirection 2
- 일시적인 리다이렉션 (302, 307, 303)
- `302 Found`: 301과 상황이 똑같다. GET 변경, 본문 제거될 수 있음 (MAY)
- `307 Temporary Redirect`: 302 기능은 같고 **요청 메서드, 본문 유지**(MUST NOT)
- `303 See Other`: 요청 메서드가 GET으로 변경(무조건), 302는 대부분 GET 변경

#### PRG
- POST - REDRIECT - GET
- POST 주문 후에 새로고침하면 중복 주문 발생 가능
- POST 주문 후 결과화면을 GET으로 리다이렉트
- POST -> `302 Found` Response (with `Location`) -> GET `Location`(주문 결과 페이지)

#### 기타
- `300 Multiple Choices`: 안쓰임
- `304 Not Modified`
  - 캐시 목적으로 사용
  - 클라이언트에게 리소스 수정되지 않았음을 알려줌(**클라이언트는 로컬 PC 캐시를 재사용**, **캐시로 리다이렉트**)
  - 메시지 바디 포함 X
  - 조건부 GET, HEAD 요청시 사용

### `4xx` Client Error, `5xx` Server Error
- 오류의 원인이 클라이언트에 있음
- 4xx: 복구 불가능(요청 자체에 문제), 5xx: 복구 가능(서버 문제가 추후에 해결 가능함)
- 4xx 종류
  - `400 Bad Request`: 클라이언트가 잘못된 요청을 해서 서버가 요청을 처리할 수 없음
  - `401 Unarthorized`: 클라이언트가 해당 리소스에 대한 인증이 필요
    - Authentication(인증) 되지 않음
    - 인증(Authentication): 본인이 누구인지 확인 / 인가(Authorization): 권한 부여(인증이 있어야 인가 가능)
  - `403 Forbidden`: 서버가 요청을 이해했지만 승인을 거부함
    - 인증은 됐는데 접근 권한이 불충분한 경우(ADMIN 권한에 대해서만 요청 허용, 나머진 Forbidden)
  - `404 Not Found`: 요청 리소스를 찾을 수 없음
- 5xx 종류
  - `500 Internal Server Error`: 서버 문제로 오류 발생, 애매하면 500 에러
  - `503 Service Unavailable`: 서비스 이용 불가
    - 일시적인 과부하 또는 예정된 작업으로 잠시 요청을 처리할 수 없음
    - `Retry-After` header 통해서 얼마뒤에 복구되는지 알려줄 수 있음

> `4xx` 에러를 `5xx` 에러로 던지지 말 것

<br>

## :pushpin: HTTP 헤더1 - 일반 헤더

### HTTP 헤더 개요

- 1999: `RFC2616` (과거)
- 2014: `RFC723x` (현재)
#### HTTP 헤더 (RFC2616 과거)
- General Header: HTTP 메시지 전체
- Request Header: 요청 정보
- Response Header: 응답 정보
- Entity Header: 엔티티 바디 정보

#### HTTP Body (RFC2616 과거)
- 메시지 본문(message body)을 통해 Entity body를 전달하는 데 사용
- Entity Header는 Entity body의 데이터를 해석할 수 있는 정보 제공
  - 데이터 유형, 데이터 길이, 압축 정보

#### `RFC723x` 변화
- Entity -> `Representation`
- `Representation` = Metadata + Data

#### HTTP Body (RFC7230 최신)
- 메시지 본문(message body)을 통해 **표현 데이터**(Data) 전달
- 메시지 본문 = `payload`
- 표현 = 요청/응답에서 전달할 실제 데이터
  - 서버에서 데이터를 어떤 형식으로 응답해줄건지(html, json, xml, ...) **표현**이 달라짐
- **표현 헤더**(Metadata): **표현 데이터**를 해석할 수 있는 정보 제공
  - **표현 메타데이터**는 헤더를 구성하는 항목 중 하나
  - `표현 헤더 = 표현 메타데이터 + (페이로드 메시지,,, 생략)`

### 표현
- `Content-Type`: 표현 데이터의 형식
  - `text/html;charset=utf-8`, `application/json`, `image/png`
- `Content-Encoding`: 표현 데이터의 압축 방식
  - `gzip`, `deflate`, `identity`
  - 데이터 읽는 쪽에서 인코딩 헤더 정보로 압축 해제
- `Content-Language`: 표현 데이터의 자연 언어
  - `en`, `ko`, `en-US`
- `Content-Length`: 표현 데이터의 길이
  - 바이트 단위
  - Transfer-Encoding(전송 코딩)사용시 Length 사용 X

### 협상(Content Negotiation)
- 클라이언트가 선호하는 표현 요청
- `Accept`: 클라이언트가 선호하는 미디어 타입 전달
- `Accept-Charset`
- `Accept-Encoding`
- `Accept-Language`
#### 우선 순위: Quality Values(q)
  - Quality Values 사용
    - 0 ~ 1 범위, 클수록 높은 우선순위(생략하면 1): `ko-KR;q=0.9`
    - ex) `Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7`
  - 구체적인 것이 우선
    - ex) `Accept: text/*, text/plain, text/plain;format=flowed, */*`
      1. `text/plain;format=flowed`
      2. `text/plain`
      3. `text/*`
      4. `*/*`

### 전송 방식
- 단순, 압축, 분할, 범위 전송으로 나뉨
- 단순 전송: `Content-Length`를 제공(한 번에 요청하고 한 번에 받기)
- 압축 전송: `Content-Encoding`, `Content-Length`
- 분할 전송: `Transfer-Encoding`
  - `Transfer-Encoding: chunked` (덩어리로 쪼개서 클라이언트에게 응답)
  - Content-Length가 없어야 한다.
- 범위 전송: `Content-Range`
  - ex) `Content-Range: bytes 1001-2000 / 2000` (2000 bytes 중 1001-2000 구간 전송)

### 일반 정보
- **From**
  - 유저 에이전트의 이메일 정보
  - 잘 사용 X, 
  - 검색 엔진 같은 곳에서 사용
  - 주로 요청 헤더
- **Referer**
  - 이전 웹 페이지 주소
  - A -> B로 이동시 `Referer: A` 정보를 헤더에 포함해서 요청
  - 요청에 사용
  - 유입 경로 분석 가능
- **User-Agent**
  - 유저 에이전트 애플리케이션 정보
  - 클라이언트 정보(웹 브라우저 정보)
  - 어떤 종류 브라우저에서 장애가 발생하는지 파악 가능
  - 요청에 사용
- **Server**
  - 요청 처리하는 ORIGIN 서버의 소프트웨어 정보
  - `ORIGIN`: 프록시 서버 말고 진짜 요청을 처리해주는 서버
  - 응답에 사용
  - ex) `Server: nginx`
- **Date**
  - 메시지가 발생한 날짜와 시간
  - 응답에 사용

### 특별한 정보
- **Host** (중요, 필수값)
  - 요청한 호스트 정보(도메인)
  - **하나의 서버가 여러 도메인을 처리해야 할 때**
  - **하나의 IP address에 여러 도메인이 적용되어 있을 때**
  - 가상 호스트를 통한 여러 도메인을 처리하는 하나의 서버, 여러 애플리케이션 구동에 여러 도메인 사용
- **Location**
  - 페이지 리다이렉션
  - `3xx` 응답시 같이 사용
  - `201 (Created)`: 여기서는 요청에 의해 생성된 새로운 리소스 URI
- **Allow**
  - `405 (Method Not Allowed)` 응답에 같이 사용
  - ex) `Allow: GET, HEAD, PUT`
- **Retry-After**
  - 유저 에이전트가 다음 요청을 하기까지 기다려야 하는 시간
  - `503 (Service Unavailable)` 서비스 불능 응답시 같이 사용

### 인증
- **Authorization**
  - 클라이언트 인증 정보를 서버에 전달
  - ex) `Authorization: Basic xxxxxx`
- **WWW-Authenticate**
  - 리소스 접근시 필요한 인증 방법 정의
  - `401 (Unauthorized)` 응답과 함께 사용

### 쿠키
- HTTP는 Stateless(무상태) 프로토콜
- 클라이언트, 서버 요청/응답 완료시 연결 끊김
- 서로 상태를 유지하지 않음
- 여기서 **쿠키**, **세션** 개념이 적용되어 보완됨
- 쿠키는 `Set-Cookie`, `Cookie` 헤더 사용

#### 인증에 사용되는 쿠키
- `set-cookie: sessionid=xxxxxxxx; expires=xxx; path=/; domain=google.com; Secure`
- 서버에서는 세션 키값을 쿠키에 넣어서 보내준다.  

#### 사용처
- 사용자 로그인 세션 관리
- 광고 정보 트래킹

#### 단점 및 사용 방법
  - 쿠키 정보는 항상 서버에 전송된다.
  - 네트워크 트래픽 추가 유발
  - 최소한의 정보만 사용해야 한다. (세션 id, 인증 토큰 등)
  - 웹 브라우저 내부 데이터를 저장할 때 **웹 스토리지**(localStorage, sessionStorage)를 활용하기도 한다.
  - 민감한 데이터는 저장하면 안됨

#### 쿠기 생명주기
- Expires
- max-age
- ex) `Set-Cookie: expires=xxxx;max-age=3600`(초 단위)

#### 쿠키 도메인
- 쿠키는 도메인을 지정할 수 있다.
- 명시한 문서 기준 도메인 + 서브 도메인 포함
  - `domain=example.org`: example.org, dev.example.org 쿠키도 접근 가능

#### 쿠키 경로
- ex) `path=/home`
- 해당 경로를 포함한 하위 경로 페이지만 쿠키 접근
- 일반적으로 루트로 지정 (`path=/`)

#### 쿠키 보안
- **Secure**
  - https만 전송 (원래는 http, https 구분 X)
- **HttpOnly**
  - XSS 공격 방지
  - javascript에서 접근 불가(`document.cookie`)
  - HTTP 전송에만 사용
- SameSite
  - XSRF 공격 방지
  - 요청 도메인과 쿠키에 설정된 도메인이 같은 경우에만 쿠키 사용