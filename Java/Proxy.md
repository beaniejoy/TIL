# Proxy 정리

## :pushpin: Weaving

Weaving은 Aspect 클래스에 정의 한 Advice 로직을 타겟에 적용하는 것  
종류로는 RTW, CTW, LTW 존재
- RTW(Run Time Weaving)
  - 자바 실행시점(런타임)에서 실행메소드 호출시 동적으로 위빙이 발생
  - Spring AOP(`JDK Dynamic Proxy`, `CGLIB`)에서 사용하는 방식
  - proxy를 생성해 실제 타깃 오브젝트의 변형없이 위빙 수행
- CTW(Compile Time Weaving)
  - 컴파일 과정에서 바이트코드 조작을 통해 직접 삽입
  - `AspectJ`에서 제공하는 AspectJ Compiler가 존재
  - 가장 빠른 성능, 하지만 lombok과 같은 플러그인과 충돌 발생
- LTW(Load Time Weaving)
  - Class Loader를 통해 JVM에 바이트코드가 로드될 때 바이트코드 조작을 통해 위빙이 이루어짐
  - 최초 런타임 시간이 오래걸림

<br> 

## Spring proxy 종류

- JDK Dynamic Proxy
- CGLib  

둘 다 런타임(동적) 위빙(weaving) 방식으로 이루어짐

### 차이점
- JDK Dynamic Proxy 
  - 인터페이스 기준으로 target의 proxy 생성
  - reflection을 통해 interface 스펙을 가져와 proxy를 생성
- CGLib
  - 클래스 기반으로 바이트코드 조작을 통해 proxy 생성


## :pushpin: References
- [AOP(4) - AspectJ](https://dahye-jeong.gitbook.io/spring/spring/2020-04-10-aop-aspectj)
- [JDK Dynamic Proxy와 CGLib를 알아보자 #2](https://velog.io/@suhongkim98/JDK-Dynamic-Proxy%EC%99%80-CGLib)