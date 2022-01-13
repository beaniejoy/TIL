# SOLID 객체지향 원칙

- Single Responsibility Principle
- Open Closed Principle
- Liskov Substitution Principle
- Interface Segregation Principle
- Dependency Inversion Principle

## SRP(단일 책임 원칙)
- Single Responsibility Principle
- **클래스를 변경해야 하는 이유는 오직 하나 뿐이어야 함**
- 어플리케이션 경계에 따라 관심 영역을 설정해주는 추상화처럼 **클래스에 하나의 역할에 대해서 정의**
- 속성, 메소드, 패키지, 모듈, 컴포넌트, 프레임워크 등에도 적용가능

## OCP (개방 폐쇄 원칙)
- **외부 모듈(클래스, 함수)의 변화에는 열려있고 모듈을 사용하는 입장에서 주변의 변화에 닫혀있어야 하는 원칙**
- JDBC 인터페이스 - 여러 벤더들이 제공해주는 모듈에 있어서 확장이 열려있고, JDBC 인터페이스는 그 변화에 닫혀있음

## LSP (리스코프 치환 원칙)
- **서브타입은 언제나 자신의 기반타입으로 교체할 수 있어야 함**
- `is a kind of` (분류도), `is able to` (인터페이스)
- 상위형 객체 참조 변수 = 하위 클래스's instance (대입 가능) > 상위 클래스의 instance 역할 문제 없음

## ISP (인터페이스 분리 원칙)
- **사용자(혹은 사용하는 객체) 입장에서 본인이 사용하지 않는 메서드에 의존 관계를 맺으면 안됨**
- SRP와 유사함. 같은 문제 다른 해결 방식. (SRP가 더 중요)
- 인터페이스는 작을수록 좋고, 상위 클래스는 풍성할수록 좋다.

## DIP (의존 역전 원칙)
- 추상화된 것은 구체적인 것에 의존하면 안됨
