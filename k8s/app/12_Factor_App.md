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

## 1. Codebase

하나의 애플리케이션은 하나의 코드 베이스를 가져야 함
(한 애플리케이션은 하나의 Git Repository에서 관리되는 그런 개념)

Source Code >> Development, Test, Production

```
application.yaml
application-development.yaml
application-production.yaml
```

<br>

## 2. Dependencies

애플리케이션에서 사용하는 종속성있는 라이브러리는 명시적으로 관리되어야 함

예전에는 필요한 라이브러리가 있으면 jar 파일을 받아서 source code 안에 포함시켜 사용했음
(빌드시 경로지정 비롯해 직접 설정)

현재는 maven, gradle 같은 도구들이 dependencies를 관리해줌  
build.gradle에 스펙만 정의해주면 build time에 외부에서 다운 받아서 함께 빌드해준다.

<br>

## 3. Config
 
설정값들이 하드코딩 되지 않고 별도의 파일로 관리되어야 함

인증키, 데이터베이스 connection 정보, api key 등 민감 데이터들을 애플리케이션 설정에 하드코딩으로 관리되어서는 안된다.  

application.yaml 설정에 관련 내용들을 지정함으로써 코드에서 하드코딩하지 않아도 관리 가능  
더 나아가서 Spring Cloud Config 통해 vault 같은 외부 인증 모듈을 사용해 관리 가능 
(네트워크 통한 민감 데이터 관리)

쿠베 내부에서 Config 내용을 따로 관리할 수 있기 때문에 최근에는 외부 Config Server를 사용한 민감 데이터 관리하는 것들이 많이 줄어듬

> 설정값 내용들은 코드와 분리되어야 한다가 핵심

<br>

## 4. Backing Service

애플리케이션 동작에 필요한 서비스들은 느슨하게 연결된 리소스로 취급

Database, Message Queue, Autheticator 등등 애플리케이션이 동작하는데 도움을 주고 필수적인 여러 서비스들은 느슨한 연결관계를 가져야 한다.  
ex) DB의 host, port, user, password만 알면 다른 DB로 바뀌어도 유연하게 적용 가능하게끔 구성 하는 것

유연하게 다른 제품으로 대체 가능한 관계를 추구  
Application은 다른 Application의 backing service가 될 수 있음 (Microservice 환경)

<br>

## 5. Build, Release, Run

코드 실행하기 위한 빌드, 릴리즈, 실행 단계를 명시적으로 구분

- build: 코드와 라이브러리를 결합해 실행가능한 패키지 만들어내는 단계
- release: 빌드 단계에서 만들어진 패키지와 설정을 결합하여 실행 직전의 상태로 만드는 단계
- run: 만들어진 릴리즈가 실행 환경에서 실행되어 서비스를 제공하는 단계
 
 <br>

 ## 6. Processes

애플리케이션이 프로세스의 형태로 실행되어야 하며 상태를 가지면 안된다.
상태 저장을 위해 애플리케이션 자체의 임시 저장소에 저장 가능하지만 영속화하기 위해서는 별도의 DB 활용해야 함

Sticky Session 같은 방법도 상태를 저장하기 위한 편법으로 취급될 수 있음. 되도록 사용하지 말자

Scale out을 고려할 때 상태를 가지지 않는 프로세스 형태의 애플리케이션이 유리할 수 있다.

<br>

## 7. Port Binding

애플리케이션은 포트를 통해 다른 애플리케이션과 상호 작용 해야 함
네트워크를 통해 서비스될 수 있어야 한다.

Request 들어올 때마다 애플리케이션이 받아서 Response 즉각 낼 수 있어야 함

현대는 더 포괄적으로 Message Queue도 들어감, 분산 애플리케이션 환경에서 MQ가 중간 다리 역할하며 서로 메시지를 주고 받으며 소통이 이루어진다는 의미도 포함
여전히 HTTP 프로토콜 형식으로 개발

<br>

## 8. Concurrency

애플리케이션을 여러 개의 프로세스로 실행하여 동시성을 가져갈 수 있음
(Scale out 가능한 형태로 만들어라)

<br>

## 9. Disposality

애플리케이션이 종료 신호에 대응할 수 있어야 하며 쉽게 종료할 수 있어야 함
Graceful shutdown 방식 & Sigterm 

<br>

## 10. Dev/prod parity

각각의 환경별로 애플리케이션의 차이는 최소화 되어야 함

예를 들어 DB 같은 것도 Dev, Prod 환경에서 거의 같게 구성해야 함

<br>

## 11. Logs

로그를 스트림 형태로 취급해서 애플리케이션 외부로 내보내고, 별도로 관리하지 말 것
로그는 콘솔에 출력하거나 파일로 출력하라

로그 자체를 가지고 애플리케이션 내부에 저장하거나 DB에 저장하거나, 로그 출력을 위한 api를 따로 둔다거나 하지 않아야 한다.

<br>

## 12. Admin Processes

관리 목적으로 사용되는 기능들은 일회성 프로세스로 실행할 것
예를 들어, 백업 목적, 자료 export, migration 작업 같은 경우 일회성 프로세스로 실행하자

서버 api 형식이 아닌, 배치형태로
메인 프로세스에 같이 실행하면 메인 서비스에 영향줄 수 있음
배치도 스케줄러에 의해 실행되는 것이 아닌 명령어를 통해 실행되는 형태
