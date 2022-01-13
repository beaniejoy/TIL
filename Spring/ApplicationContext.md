# ApplicationContext

- BeanFactory를 구현
- 스프링 IoC 컨테이너의 핵심

## Spring IoC Container

> `IoC (제어의 역전)`  
클라이언트가 직접 객체를 생성하고 의존성을 주입해주고 실행하고 모든 역할에 대해 담당했다면 객체 생성하는 역할을 외부에 위임함으로써 역할을 분리  
(객체지향 SRP 원칙 준수)

의존 객체를 직접 만들어 사용X -> 외부에서 만들어진 오브젝트를 주입 받아 사용

- Bean: 스프링 IoC 컨테이너가 관리하는 객체를 지칭
- BeanFactory가 최상위 인터페이스
- ApplicationContext는 이를 확장한 오브젝트

### 장점
- 의존성 관리
- 스코프 관리
- LifeCycle 관리

