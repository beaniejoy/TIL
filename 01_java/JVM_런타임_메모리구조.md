# JVM & Java Runtime 메모리 구조

JVM
- Class Loader
- Execution Engine
- Garbage Collection
- Runtime Data Area
  - Method Area
  - Heap
  - Stack
  - PC Register
  - Native Method Stack

## JVM 구조

### Class Loader

class 파일 자체가 바이트코드라 해서 기계가 이해하는 기계어가 아니다.  
이를 JVM위에 올려 기계가 이해할 수 있는 언어로 바꾸어주어 작동시켜주어야 한다.  
Class Loader가 `.class` 파일들을 엮어 Runtime Data Area에 각 영역으로 적재해준다.

### Execution Engine

Runtime Data Area 메모리영역에 적재된 클래스파일(바이트코드)들을 기계어로 번역하고 명령어 단위로 실행.

- interpreter 방식: 명령어 하나하나 실행
- **JIT Compiler(Just-In-Time)**
    - 전체 바이트코드를 네이티브 코드로 변경해서 Execution Engine이 **네이티브**로 컴파일된 코드를 실행하는 것으로 성능을 높이는 방식
    - 네이티브로 실행한다는 것은 JVM이라는 중간매개체가 없이 컴퓨터가 바로 실행할 수 있는 코드 (ex. C언어)

### GC(Garbage Collection)

참조되지 않는 객체들을 탐색 후 제거해주는 역할. 뒤에서 언급. 

### Runtime Data Area

여기도 세부적으로 나눌 수 있다.