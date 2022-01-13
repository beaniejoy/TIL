# Gradle Command


```bash
$ ./gradlew clean build --exclude-task test
```
- test 과정 없이 기존의 build 내용 clean 후 다시 build 진행

```bash
$ gradle wrapper (IDE 도움 받을 것)

$ ./gradlew wrapper --gradle-version <VERSION>
```
- gradle 버전 변경

```bash
$ ./gradlew help --scan
$ ./gradlew help --warning-mode=all
```
- gradle 버전과 각종 dependencies 버전, 자바, 코틀린 버전 호환성 검사