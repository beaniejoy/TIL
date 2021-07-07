# JIT Compiler

- Just in Time  
- JVM은 기본적으로 Execution Engine에 의해 바이트코드를 기계어로 번역하는데 interpreter 방식으로 수행함.
- interpreter 방식은 속도가 느림
- 이를 보완하기 위해 고안된 컴파일러
- 바이트코드 -> Native 코드롤 변환하는 작업 수행
- JVM내 별도 쓰레드에서 수행

## Hotspot JVM
Hotspot JVM은 JIT Compiler와 관련성이 높다.

## Graal VM