# QueryDSL Gradle 설정

- QueryDSL Annotation Processor 설정에 관한 Gradle 내용

## Gradle 4.x 설정
```gradle
def generatedDir = file("src/main/java-generated")

sourceSets {
    main {
        java {
            srcDir generatedDir
        }
    }
}

task generateQuerydsl(type: JavaCompile, group: 'build') {
    doFirst {
        if (!generatedDir.exists()) {
            logger.info("Creating `$generatedDir` directory")

            if (!generatedDir.mkdirs()) {
                throw new InvalidUserDataException("...")
            }
        }
    }

    source = sourceSets.main.java
    classpath = configurations.compile
    options.compilerArgs = [
            "-proc:only",
            "-processor",
            "com.querydsl.apt.jpa.JPAAnnotationProcessor"
    ]
    destinationDir = generatedDir
}

compileJava.dependsOn(generateQuerydsl)

clean {
    delete generatedDir
}
```
- `JPAAnnotationProcessor`를 `JavaCompile`에서 직접 지정해주어 사용
- Annotation Processor 뿐만 아니라 생성된 클래스 관리 디렉토리도 직접 지정해야 하는 이슈 존재
- `querydsl-apt` 외에 `Lombok` 등 Annotation Processor 를 사용하는 라이브러리가 추가될 때마다 `-processor` 에 클래스를 추가해줘야하는 번거로움이 존재

<br>

## Gradle 4.x 버전 이후

```gradle
// QueryDSL plugin
implementation "com.querydsl:querydsl-jpa:${queryDslVersion}"
annotationProcessor "com.querydsl:querydsl-apt:${dependencyManagement.importedProperties['querydsl.version']}:jpa"
annotationProcessor "jakarta.persistence:jakarta.persistence-api"
annotationProcessor "jakarta.annotation:jakarta.annotation-api"
```
- gradle 4.x 이후부터 사용되는 방식
- 별다른 설정을 하지 않아도 그레이들 자체에서 `annotationProcessor` 으로 선언한 라이브러리의 적절한 AnnotationProcessor 를 선택하여 사용
- IntelliJ 자체 설정을 통해 Gradle 빌드를 Gradle 자체로 할 것인지 IntelliJ IDE에 맡길 것인지에 따라 `Querydsl Annotation processor`가 생성하는 클래스 생성물 위치가 달라짐
  - **Gradle**: `build/generated`
  - **IntelliJ**: `src/main/generated`