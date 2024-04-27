# 12 Factor App

SaaS 애플리케이션을 잘 만들기 위한 원칙들을 모아 놓은 방법론  
뿐만 아니라 모던 백엔드 개발에 필요한 원칙들도 포함  
2010년 기점

Classical Backend Developement >> Modern Backend Development(SaaS)

- Modern Backend Development(SaaS)
  - Open Source Software
  - Cloud Environment

MSA, Cloud Native Programming

<br>

## Codebase

하나의 애플리케이션은 하나의 코드 베이스를 가져야 함
(한 애플리케이션은 하나의 Git Repository에서 관리되는 그런 개념)

Source Code >> Development, Test, Production

```
application.yaml
application-development.yaml
application-production.yaml
```

<br>

## Dependencies

애플리케이션에서 사용하는 종속성있는 라이브러리는 명시적으로 관리되어야 함

예전에는 필요한 라이브러리가 있으면 jar 파일을 받아서 source code 안에 포함시켜 사용했음
(빌드시 경로지정 비롯해 직접 설정)

현재는 maven, gradle 같은 도구들이 dependencies를 관리해줌  
build.gradle에 스펙만 정의해주면 build time에 외부에서 다운 받아서 함께 빌드해준다.

<br>

## Config
 
설정값들이 하드코딩 되지 않고 별도의 파일로 관리되어야 함

인증키, 데이터베이스 connection 정보, api key 등 민감 데이터들을 애플리케이션 설정에 하드코딩으로 관리되어서는 안된다.  

application.yaml 설정에 관련 내용들을 지정함으로써 코드에서 하드코딩하지 않아도 관리 가능  
더 나아가서 Spring Cloud Config 통해 vault 같은 외부 인증 모듈을 사용해 관리 가능 
(네트워크 통한 민감 데이터 관리)

쿠베 내부에서 Config 내용을 따로 관리할 수 있기 때문에 최근에는 외부 Config Server를 사용한 민감 데이터 관리하는 것들이 많이 줄어듬

> 설정값 내용들은 코드와 분리되어야 한다가 핵심

<br>

## Backing Service

애플리케이션 동작에 필요한 서비스들은 느슨하게 연결된 리소스로 취급

Database, Message Queue, Autheticator 등등 애플리케이션이 동작하는데 도움을 주고 필수적인 여러 서비스들은 느슨한 연결관계를 가져야 한다.  
ex) DB의 host, port, user, password만 알면 다른 DB로 바뀌어도 유연하게 적용 가능하게끔 구성 하는 것

유연하게 다른 제품으로 대체 가능한 관계를 추구  
Application은 다른 Application의 backing service가 될 수 있음 (Microservice 환경)

<br>

## Build, Release, Run

코드 실행하기 위한 빌드, 릴리즈, 실행 단계를 명시적으로 구분

- build: 코드와 라이브러리를 결합해 실행가능한 패키지 만들어내는 단계
- release: 빌드 단계에서 만들어진 패키지와 설정을 결합하여 실행 직전의 상태로 만드는 단계
- run: 