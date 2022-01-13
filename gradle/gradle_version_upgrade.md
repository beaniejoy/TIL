# Gradle 설정 (Spring Boot Project)

## Wrapper

- Spring Boot 내부에서는 `./gradlew`로 gradle 명령을 수행할 수 있다.
```cmd
$ ./gradlew
```
- 이 때 다음과 같은 에러 발생할 수 있다.
```text
오류: 기본 클래스 org.gradle.wrapper.GradleWrapperMain을(를) 찾거나 로드할 수 없습니다.
```
- `gradle/wrapper/gradle-wrapper.jar` 파일이 없어서 발생한 문제점이다.
```cmd
$ gradle wrapper
```
- 위 명령이 실행되지 않으면(gradle이 로컬이 설치되어 있지 않아서) IntelliJ 오른쪽 gradle 탭에서 wrapper 작업 수행하면 된다.
- 위 작업을 수행하면 해당 jar 파일이 생성될 것이고 `./gradlew` 실행이 잘 진행될 것이다.

## Gradle Version Update

> - target: `4.10.2` -> `5.x` -> `6.x` 단계별로 진행 예정  
> - [gradle doc](https://docs.gradle.org/current/userguide/upgrading_version_4.html#upgrading_version_4)  
> - Spring Boot, Kotlin version도 업그레이드 필요 (IntelliJ와 macOS 새로운 버전과 호환성 문제 때문)

### 1단계
- 프로젝트 스펙 살펴보기
```cmd
$ ./gradlew help --scan

$ ./gradlew help --warning-mode=all
```
- 여기서 `deprecations view`도 확인할 수 있다.

### 2단계
- 이전 단계에서 프로젝트 plugin 중에서 deprecation이 있다면 업데이트가 필요하다.
- 만약 문제가 되는 부분이 없다면 다음 과정 진행하면 된다.
```cmd
$ ./gradlew wrapper --gradle-version 5.0
```
- gradle-wrapper.properties에 5.0 버전이 반영되어 있을 것이다.
- `./gradlew wrapper`를 실행해주어 jar 파일을 한번더 빌드해주자(업그레이드 하면 자동으로 생성해주지만 혹시나) 

### 3단계
- gradle version `4.10.2` -> `5.0`
- version up 이후 deprecation 새로 발생한 것이 있는지 체크
```
> Configure project :
The DefaultSourceDirectorySet constructor has been deprecated. ~~~
The ProjectLayout.directoryProperty() method has been deprecated. ~~~
The DefaultTask.newOutputFile() method has been deprecated. ~~~
The DefaultTask.newInputDirectory() method has been deprecated. ~~~
The DefaultTask.newInputFile() method has been deprecated. ~~~
```
- `gradle-docker-plugin`, `kotlin version` 관련 이슈 발생
- The DefaultSourceDirectorySet constructor has been deprecated.
  - kotlinVersion = `1.3.11` -> `1.3.21`

- `com.bmuschko:gradle-docker-plugin:4.3.0` -> `4.7.0`
  - gradle `5.0` -> `5.6`
  - [Fix gradle 5.x deprecation warnings](https://bmuschko.github.io/gradle-docker-plugin/current/user-guide/#usage_3)
  - [gradle 5.6](https://github.com/bmuschko/gradle-docker-plugin/issues/866)
- 최종적으로 `gradle 5.6`, `kotlin 1.3.21`, `gradle-docker-plugin 4.7.0` 으로 버전 업
- 아직 여러 문제가 존재
  - snakeyaml 호환 X 문제, Getter lombok 문제

### 4단계

```gradle
compile "org.springframework.boot:spring-boot-starter-validation"
```
- Spring Boot `2.3.x` 부터 `spring-boot-starter-web`과 `validation`이 분리
- 위의 validation 의존성 따로 설정해야 함

```gradle
// build.gradle
compile "org.projectlombok:lombok:1.18.12"
annotationProcessor "org.projectlombok:lombok"
```
- gradle 버전 업 되면서 어노테이션 설정에도 변경이 필요
- lombok에 `annotationProcessor` 따로 지정해야 lombok 관련 어노테이션 적용 가능

#### Error

```
A bean with that name has already been defined in class path resource
```
- 특정 Configuration 파일 안에 특정 자바 클래스 객체가 중복되어 있다는 에러로 실행이 불가한 상황
- 확인해보니 serverProfile 관련 클래스가 중복으로 설정이 되어 있는 것을 확인 (이전 버전에서는 왜 중복 에러가 나오지 않았는지)

```yaml
spring:
  main:
    allow-bean-definition-overriding: true
```
- 중복되는 Bean 중 하나를 제거하거나 위의 방식으로 yaml 설정 추가하면 된다. (팀 협의 필요)

```
Unable to locate Attribute  with the the given name [XAccountId]
```
- Entity 클래스에 `XAccountId` 로 String field 설정되어 있음
- JPA query method로 `findByUserIdOrXAccountIdOrderByIdDesc(...)` 이런 식으로 설정되어 있는데 JPA에서 인식 오류가 있는 것으로 추정
