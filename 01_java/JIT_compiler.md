# JIT Compiler

> The JIT compiler's role is to turn class files (composed of bytecode, which is the JVM's instruction set) into machine code that the CPU executes directly.  
> (redhat developer)
- **JIT Compiler는 기본적으로 bytecode로 구성된 class 파일을 CPU가 이해할 수 있는 기계어로 변환해주는 역할 수행** (내가 가장 착각하고 있던 부분)

## Basic

- Just in Time  
- JVM은 기본적으로 Execution Engine에 의해 바이트코드를 기계어로 번역하는데 interpreter 방식으로 수행함.
- interpreter 방식은 속도가 느림
- 이를 보완하기 위해 고안된 컴파일러
- 바이트코드 -> Native 코드롤 변환하는 작업 수행
- JVM내 별도 쓰레드에서 수행
- 네이티브 코드로 변환되기 때문에 인터프리터 방식보다 더 빠르게 수행

## Hotspot JVM
- Hotspot JVM은 JIT Compiler와 관련성이 높다.
- **JIT Compiler 기술의 한 가지 구현체**
- interpreter를 실행하며 JVM 구동하고 앱의 실제 성능을 감시
- 자주 사용되는 코드에 대해서 네이티브 코드로 완전히 컴파일되고 캐시

## References
- [JIT Compiler in OpenJDK(redhat developers)](https://developers.redhat.com/articles/2021/06/23/how-jit-compiler-boosts-java-performance-openjdk#the_jit_compiler_in_openjdk)
- [JIT Compiler의 기본개념과 내부동작](https://inspirit941.tistory.com/352)