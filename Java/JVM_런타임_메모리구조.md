# JVM & Java Runtime 메모리 구조

JVM
- Class Loader
- Execution Engine
- Garbage Collection
- Runtime Data Area
  - Method Area > `Thread 공유`
  - Heap > `Thread 공유`
  - Stack
  - PC Register
  - Native Method Stack

## JVM 구조

- 자바 바이트 코드(`.class`) 실행 주체
- CPU, OS종류에 관계없이 실행 가능하게 해준다.

![img1 daumcdn](https://user-images.githubusercontent.com/41675375/124784848-49640900-df81-11eb-9022-09e4eae51096.png)

### Class Loader

class 파일 자체가 바이트코드라 해서 기계가 이해하는 기계어 X
이를 JVM위에 올려 기계가 이해할 수 있는 언어로 바꾸어주어 작동시켜주어야 한다.  
Class Loader가 `.class` 파일들을 엮어 Runtime Data Area에 각 영역으로 적재해준다.

### Execution Engine

Runtime Data Area 메모리영역에 적재된 클래스파일(바이트코드)들을 기계어로 번역하고 명령어 단위로 실행.

- interpreter 방식: 명령어 하나하나 실행
- **JIT Compiler(Just-In-Time)**
    - 전체 바이트코드를 네이티브 코드로 변경해서 Execution Engine이 **네이티브**로 컴파일된 코드를 실행하는 것으로 성능을 높이는 방식
    - 네이티브로 실행한다는 것은 JVM이라는 중간매개체가 없이 컴퓨터가 바로 실행할 수 있는 코드 (ex. C언어)

### GC(Garbage Collection)

- Heap 메모리 영역에서 참조되지 않은 객체들을 탐색 후 제거하는 역할.  
- GC가 동작하는 시간은 정확히 언제인지 알 수 없음.
- GC가 수행되는 동안 모든 쓰레드가 일시정지. (`Full GC` -> 치명적 문제의 원인)

### Runtime Data Area

#### Method Area

- 클래스 맴버변수(필드) 정보: 맴버변수 이름, 데이터 타입, 접근제어자
- 메서드 정보: 메서드 이름, 파라미터 정보, 접근 제어자
- Type 정보: `class` or `interface`
- Constant Pool: 문자 상수, 타입, 필드, 객체 참조
- static 변수
- final class 변수

위 데이터들이 생성되는 영역

#### Heap Area

- new 생성자로 생성된 객체, 배열 저장
- Method Area에 Load된 클래스만을 대상으로 한 객체들이 담긴다.
- GC 영역

#### Stack Area

- 지역변수, 파라미터, 리턴 값, 연산을 위한 임시저장 값 등이 생성
```java
Example ex = new Example();
// ex(힙 영역의 주소값 포함) > Stack
// Example instance > Heap
```

#### PC Register

- CPU Register와 다름
- Thread가 생성될 때마다 여기서 생성
- Program Counter: Current Thread's 주소, 명령을 저장

#### Native Method Stack

네이티브 코드를 수행하기 위한 스택 장소
(C, C++ -> JNI)


## JDK8 버전에서의 메모리 구조 변화

- JDK8 이전
```
<----- Java Heap ----->             <--- Native Memory --->
+------+----+----+-----+-----------+--------+--------------+
| Eden | S0 | S1 | Old | Permanent | C Heap | Thread Stack |
+------+----+----+-----+-----------+--------+--------------+
                        <--------->
                       Permanent Heap
S0: Survivor 0
S1: Survivor 1
```
- JDK8 이후
```
<----- Java Heap -----> <--------- Native Memory --------->
+------+----+----+-----+-----------+--------+--------------+
| Eden | S0 | S1 | Old | Metaspace | C Heap | Thread Stack |
+------+----+----+-----+-----------+--------+--------------+
```

- JDK8 이전에는 PermGen 영역의 제한으로 JVM이 프로세스를 돌릴 때 메모리 상한선이 존재했음
- 이 상한선을 넘는 코드작업이 이루어지면 `java.lang.OutOfMemoryError` 에러발생으로 프로세스가 죽어버리는 일이 자주 발생
- PermGen영역을 없애고 Metaspace를 적용해 Native 메모리 영역에서 관리되도록 했음
- Native 메모리 영역은 OS가 자동으로 크기를 조절, 자바 개발하면서 메모리 크기에 대한 상한선을 고려하지 않아도 됨
- 기존 PermGen에 속해있던 `static Object`는 Heap 영역으로 이동 -> **GC의 대상이 되었음**

### Permanent Generation (PermGen)
```
The JVM keeps track of loaded class metadata in the PermGen.
```
- JDK 7버전까지 있었던 PermGen 영역에서 관리했던 내용은 다음과 같다.
  - static methods
  - primitive variables
  - references to the static object
  - String Pool도 permgen에서 관리
  - (bytecode data)
  - (JIT information)
- default max memory size: 
  - 32-bit JVM - 64MB
  - 64-bit JVM - 82MB
- option(deprecated)
  - `-XX:PermSize=[size]`: initial or minimum size setting
  - `-XX:MaxPermSize=[size]`: maximum size setting
- 여기서 `OutOfMemoryError`가 자주 발생했었음


## Reference
- https://jeong-pro.tistory.com/148 
- [PermGen, Metaspace로의 구조 변화](https://johngrib.github.io/wiki/java8-why-permgen-removed/)