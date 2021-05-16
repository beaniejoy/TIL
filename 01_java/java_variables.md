# Java Variables

자바 변수

## 변수의 종류

- 지역 변수(local variables)  
  선언한 중괄호 내에서만 유효
- 매개 변수(parameters)  
  메서드 호출 - 종료 시점까지 유효
- 인스턴스 변수(instance variables)  
  객체 생성 - GC에 의한 소멸
- 클래스 변수(class variables)  
  클래스 처음 호출 - 자바 프로그램 종료

```java
public class SmartPhone {
  // 인스턴스 변수(맴버 변수)
  private int size;
  private String number;
  private String os;

  // 클래스 변수
  public static int status;

                  // 매개변수
  public void call(String toNumber) {
    // 지역 변수
    int areaCode;
  }
}
```

## 자료형(Data Type)

- 기본 자료형(Primitive Type)
  -  byte, short, int, long, char
  -  float, double
  -  boolean
- 참조 자료형(Reference Type)
  - Object
  - Array

## 자료형 Default

- byte: 0
- short: 0
- int: 0
- long: 0
- float: 0.0
- double: 0.0
- char: '₩u0000'
- boolean: false 