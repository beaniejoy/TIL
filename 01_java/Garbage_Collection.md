# Garbage Collection (GC)

### `stop-the-world`

GC를 실행하기 위해 JVM이 애플리케이션 실행을 멈춘다.  
GC를 실행하는 쓰레드를 제외한 나머지 쓰레드는 모두 작업을 멈추게 된다.

**GC 튜닝** -> `stop-the-world` 시간을 줄이는 작업  
GC 튜닝은 Minor GC 영역을 늘린다던가, 두 개의 Survivor 영역간에 객체 이동시 count 기준을 달리 설정해서 가능

### GC가 지울 대상을 결정하는 기준
- Reference Counter

### `weak generational hypothesis`

**GC의 기본 전제 조건**
- 대부분의 객체는 금방 접근 불가능 상태(unreachable)가 된다.
- 오래된 객체에서 젊은 객체로의 참조는 아주 적게 존재한다.


## 구조

### Young Generation 영역
- 새로 생성된 객체 대부분이 여기에 위치
- 이 영역에서 객체가 사라질 때 `Minor GC` 발생
- Eden 영역, Survivor 영역(2개)으로 구성

### Old Generation 영역
- Young Gen에서 살아남은 객체가 여기로 복사
- 대부분 Young 영역보다 크게 할당
- 크기가 큰 만큼 Young 보다는 적은 GC 발생
- 이 영역에서 객체가 사라질 때 `Major GC`(or `Full GC`) 발생

### Permanent Generation 영역
- PermGen
- Method Area라고도 함
- 객체나 문자열 정보 저장하는 장소
- 이름처럼 영원히 남아있는 곳은 아님
- 여기서 GC가 발생할 수 있는데, Major GC 횟수에 포함
- JDK7 까지 해당, JDK8 부터는 `metaspace`로 바뀜

### Card Table
- Old 영역에 있는 객체가 Young 영역 객체를 참조하는 경우가 발생한 경우 사용
- Old 영역에 512 bytes 덩어리로 존재
- Old 영역에 있는 객체가 Young 영역 객체를 참조할 때마다 여기에 정보가 표시
- Minor GC가 발생할 때 Old 영역의 모든 객체 참조를 확인 X -> 카드 테이블을 참조(여기서 GC대상 구별)


## 각 영역에서의 GC 발생

### Young 영역

- 처음 새로 생성한 대부분 객체가 Eden 영역에 위치
- Eden 영역에서 GC 발생 후 살아남은 객체 -> Survivor 영역 중 하나로 이동
- 이런 방식으로 Survivor에 계속 객체가 쌓임
- 하나의 Survivor 영역이 가득차면 그 중에서 살아남은 객체를 다른 Survivor 영역으로 이동  
  - 기존의 가득찬 Survivor는 빈 상태로 돌아감  
  - Survivor 영역 두 개 중 하나는 반드시 비어있는 상태여야 한다.  
  - 두 영역 모두 0이거나 모두 데이터가 존재하는 경우 비정상적인 시스템 상태
- 이 과정을 반복하면서 계속 살아남아 있는 객체는 Old 영역으로 이동

### HotSpot VM

- `bump-the-pointer`
- `Thread-Local Allocation Buffers(TLABs)`

### Old 영역


## Reference
- [GC 설명(Naver)](https://d2.naver.com/helloworld/1329)
- [GC Algorithms 종류](https://medium.com/@joongwon/jvm-garbage-collection-algorithms-3869b7b0aa6f)