# REST API

- REST 아키텍처의 제약조건을 준수하는 애플리케이션 프로그래밍 인터페이스
- `RE`presentational `S`tate `T`ransfer

## API
- `A`pplication `P`rogramming `I`nterface
- 애플리케이션 소프트웨어를 구축하고 통합하는 정의, 프로토콜 세트
- `정보 제공자 - 정보 사용자` 간 `호출 - 응답` 구조를 구성
- 컴퓨터, 시스템과 상호 작용해서 정보를 검색하거나 기능을 수행하고자 할 때 API가 조정자 역할을 함
<img width="770" alt="스크린샷 2021-12-07 오후 10 05 19" src="https://user-images.githubusercontent.com/41675375/145034991-52e4fa73-f32f-4c6d-8329-3e1ec708ef23.png">
- `유연성`: 명칭에도 알 수 있듯 `interface` 역할을 한다는 것이 핵심이라 생각
  - API를 사용하는 이용자는 API 내부 동작 방식을 알지도 못하고 알 필요도 없는 상태로 요청을 보내는 방식만 알면 원하는 리소스를 응답으로 받을 수 있음
  - 사용 방법을 간소화 함으로써 여러 다양한 서비스들을 연결하고 API 내부를 수정해도 외부에는 영향을 주지 않는다는 점에서 유연한 관리가 가능

## SOAP API
- `S`imple `O`bject `A`ccess `P`rotocol
- API에서 REST API와 더불어 같이 언급되는 SOAP API
- 보안이나 메시지 전송에 있어 REST보다 더 많은 표준들이 정해져 있음
- 보안, 트랜잭션, ACID원칙을 준수해야 하는 기능들에 적합
- 보안수준이 엄격해서 금융관련 서비스, 메시징 앱 등에서 사용되고 있음
- SOAP 표준에 성공/반복 실행 로직이 규정되어 있기 때문에 이를 통해 통신을 하는 경우 끝까지 신뢰성을 제공하게 됨
- 빌트인 룰을 적용하고 있기 때문에 오버헤드가 존재, 페이지 로드 시간이 길어질 수 있음

## REST API
- `RE`presentational `S`tate `T`ransfer
- URI와 HTTP 프로토콜 기반으로 함(클라이언트-서버 모델로 구축)
- 단순하다는 장점이 있음(단일한 인터페이스를 사용하기 때문에 동일한 경로를 통해서 접속)

### 특징
- 클라이언트 - 서버 아키텍처에서 작동
- Uniform interface로 작동
  - URI로 지정한 리소스에 대한 조작을 일관된 방식으로 수행하는 아키텍처 스타일
- Stateless한 특징이 있음
  - 작업을 위한 상태정보를 따로 저장하지 않는다.
  - 세션, 쿠키정보를 따로 저장하고 관리하는 것이 아닌 요청, 응답을 통해 정보를 주고받기만 함
- 데이터 캐시 기능 제공
  - REST 방식은 HTTP 방식을 그대로 사용
  - [HTTP가 가진 캐싱 기능](https://hahahoho5915.tistory.com/33)을 적용 가능 
- 여러 테이터 포멧을 다룰 수 있음
  - HTML, TEXT, JSON, XML 등...
  - REST 방식은 JSON 방식을 선호(브라우저간 호환성이 좋음)
- 계층형 구조
  - 다중 계층으로 구성할 수 있기 때문에 **유연한 구조**를 가질 수 있음
  - 보안, 로드 밸런싱, 암호화 계층을 추가한다거나 Proxy, Gateway같은 네트워크 기반 중간매체 사용 가능

## SOAP, REST 방식의 차이

|  | SOAP | REST 
 :---: | :---: |:---:
 유형 | Protocol | Architecture
 데이터포멧 | XML | Text, HTML, XML, JSON, ...
 보안 | WS-Security, SSL | [HTTPS](https://github.com/beaniejoy/TIL/blob/main/06_web/https.md), SSL
 연결 | 긴밀한 연결, 통신 방식이 엄격 | 추상화되어 있어 느슨한 연결 형태

## REST API 구성 요소
- 자원(Resource)
  - 리소스는 서버에 존재하고 모든 자원에 고유한 ID 존재
  - HTTP URI를 통해 자원을 구별(`/api/v1/cafes/[cafe:id]`)
  - 클라이언트는 URI를 통해 원하는 자원을 설정하고 이를 조회, 조작하는 요청을 서버에 보낼 수 있음
- 행위(Verb)
  - HTTP에 기반하고 있기 때문에 HTTP프로토콜의 Method를 사용한다.
  - GET, POST, PUT, PATCH, DELETE 기능 제공
- 표현(Representation of Resource)
  - 클라이언트가 자원에 대한 내용을 요청하면 서버는 이에 대한 작업을 진행해주고 적절한 응답을 보냄
  - 하나의 자원은 JSON, XML, TEXT 등 여러 형태로 응답 표현(JSON이 대세)

## 설계 규칙

- 정보의 자원을 표현해야 함 (`/members/1`)
- 행위(Method)는 URL에 포함 X (`/memeber/get/1`, `/delete-cafe` X)
- `-`(dash) 사용, `_`(underbar) 사용 X (`/hello_java` X)
- 마지막에 `/` 포함 X (`/users/1/` -> `/users/1`)
- 대문자보다는 소문자가 적합


## 출처
- [RedHat - API란 (개념, 기능, 장점)](https://www.redhat.com/ko/topics/api/what-are-application-programming-interfaces)
- [위시캣 - SOAP vs REST 차이](http://blog.wishket.com/soap-api-vs-rest-api-%EB%91%90-%EB%B0%A9%EC%8B%9D%EC%9D%98-%EA%B0%80%EC%9E%A5-%ED%81%B0-%EC%B0%A8%EC%9D%B4%EC%A0%90%EC%9D%80/)
- [RESTful API 설계 가이드 - 상세한 설명 Good](https://sanghaklee.tistory.com/57)