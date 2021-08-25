# Instant


```java
Instant epoch = Instant.EPOCH;
Instant.ofEpochSecond(0); // same;
```
- UTC 기준 `1970 1/1 0:0:0` > `Instant.EPOCH`로 설정되어있음

## String, Instant간 Converting

```java
String[] splitStr = s.split(" ");
String dateTime = splitStr[0] + "T" + splitStr[1] + "Z";
Instant finishTime = Instant.parse(dateTime);
```
- `2016-09-15 01:00:04.001`(String) > `2016-09-15T01:00:04.001Z`(Instant)
- 중간에 날짜, 시간사이에 `T` / 맨 마지막에 `Z`가 있어야 `Instant`로 파싱이 된다.