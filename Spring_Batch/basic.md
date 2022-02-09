# Spring Batch Basic

- fastcampus 강의 필기내용
- [my github repo](https://github.com/beaniejoy/test-project-repository/tree/main/_courses/spring-batch-basic)

<br>

## 목차

- Job
- Step
- Spring Batch 관련 테이블 구조
- ExecutionContext
- Chunk
- JobParmeters

<br>

## :pushpin: Job

- Job : spring batch의 실행 단위
- Step : Job의 실행 단위

```java
// HelloConfiguration.java
private final JobBuilderFactory jobBuilderFactory;

public HelloConfiguration(JobBuilderFactory jobBuilderFactory) {
    this.jobBuilderFactory = jobBuilderFactory;
}

@Bean
public Job helloJob() {
    return jobBuilderFactory.get("helloJob")
            .incrementer(new RunIdIncrementer())
            .start(helloStep()) // step 설정 내용
            .build();
}
```

- `JobBuilderFactory`
  - `get("job_name")`
    - job의 이름
    - spring batch를 실행시켜줄 수 있는 key
  - `start("step_name")`
    - job 실행시 최초로 수행할 step 지정
    - `StepBuilderFactory`에 의해 생성된 Step 객체를 넣어준다.
- `RunIdIncrementer`
  - job이 생성될 때마다 파라미터 id를 자동으로 생성해주는 클래스

### 특정 Job 실행

- 여러 job을 설정하고 애플리케이션을 그냥 실행하면 모든 배치가 실행이 된다.
- 선택적으로 배치를 실행할 수 있어야하고 평소에는 배치가 실행되지 않도록 하는 것이 좋다.

```shell
--spring.batch.job.names=[job_name]
```

- `[job_name]`을 실행 argument에 지정함으로써 특정 Job만 배치실행 가능

```yaml
spring:
  batch:
    job:
      names: ${job.name:NONE}
```

- 이렇게 해서 `--job.name=[job_name]` argument로 실행하면 특정 job만 실행 가능
- `job.name` argument 없이 실행하면 실행되는 배치가 없도록 설정됨

<br>

## :pushpin: Step

```java
@Bean
public Step helloStep() {
    return stepBuilderFactory.get("helloStep")
            .tasklet((contribution, chunkContext) -> {
                log.info("hello spring batch!");
                return RepeatStatus.FINISHED;
            })
            .build();
}
```

- `StepBuilderFactory`를 통해 Step 생성
- Job의 세부 실행 단위, N개가 등록돼 실행
- Step의 실행 단위
  - Task
    - 하나의 작업 기반
    - 10000개 데이터를 한꺼번에 해도 무리가 없다고 판단할 때 Task로 작업
  - Chunk
    - 하나의 큰 덩어리를 n개씩 나눠서 실행
    - 10000개 데이터 한꺼번에 하기엔 무리 -> 1000개씩 나눠서 실행
  - Task도 Chunk처럼 페이징 처리할 수 있지만 명확히 책임을 나눠서 실행할 수 있다.
    - Chunk 기반 Step : `ItemReader`, `ItemProcessor`, `ItemWriter`

<br>

## :pushpin: Spring Batch 관련 테이블 구조

- Spring Batch 관련 메타데이터를 담는 테이블 구조를 제공함
- 스프링 배치를 실행하고 관리하기 위한 테이블
- `spring-batch-core/org.springframework/batch/core/*` 여기에 위치
- `schema-xxx.sql` : database에 따라 DDL 쿼리 제공
- yaml 설정파일에 다음과 같이 스키마 실행 설정을 할 수 있다.

```yaml
spring:
  batch:
    jdbc:
      initialize-schema: never
```

- `spring.batch.jdbc.initialize-schema`로 변경
  - `always`(개발)
  - `embedded`(개발/h2 같은 인메모리)
  - `never`(운영 환경일 때 추천, 테이블이 적용안되어있으면 직접 `schema.sql`을 실행해야함)
- default 값은 `embedded`

### Batch 관련 메타데이터 테이블(메타 테이블)

- `BATCH_JOB_INSTANCE`
  - job이 실행되고 생성되는 최상위 계층 테이블
  - job_name, job_key 기준으로 하나의 row 생성
  - 같은 job_name, job_key가 저장될 수 없다.
  - `incrementer`에 `RunIdIncrementer`를 지정하면 같은 내용이어도 새로 instance 생성
  - `JobInstance` 클래스와 매핑
- `BATCH_JOB_EXECUTION`
  - job 실행되는 동안 시작/종료 시간 job 상태 등 관리
  - `JobExecution` 클래스와 매핑
- `BATCH_JOB_EXECUTION_PARAMS`
  - job 실행을 위해 주입된 param 정보 저장
  - `JobParameters` 클래스와 매핑
- `BATCH_JOB_EXECUTION_CONTEXT`
  - job이 실행되며 공유해야할 데이터 직렬화 저장
  - job 안의 모든 step들이 공유해서 사용 가능
  - `ExecutionContext` 클래스와 매핑
- `BATCH_STEP_EXECUTION`
  - step이 실행되는 동안 필요한 데이터 또는 실행된 결과 저장
- `BATCH_STEP_EXECUTION_CONTEXT`
  - step이 실행되며 공유해야할 데이터 직렬화 저장
  - 같은 step 안에서 공유해서 사용 가능(다른 step에서는 공유 X)

<br>

## :pushpin: ExecutionContext

```java
@Bean
public Step shareStep(){
        return stepBuilderFactory.get("shareStep")
          .tasklet((contribution,chunkContext)->{
            StepExecution stepExecution=contribution.getStepExecution();
            ExecutionContext stepExecutionContext=stepExecution.getExecutionContext();

            JobExecution jobExecution=stepExecution.getJobExecution();
            JobInstance jobInstance=jobExecution.getJobInstance();
            JobParameters jobParameters=jobExecution.getJobParameters();
            ExecutionContext jobExecutionContext=jobExecution.getExecutionContext();

            return RepeatStatus.FINISHED;
          })
          .build();
        }
```

- `Tasklet` interface의 execute 메서드에서 `contribution(StepContribution)` 인자를 통해 Context 객체를 가져온다.
- `StepContribution` -> `StepExecution` -> `ExecutionContext`(Step)
- `StepExecution` -> `JobExecution` -> `JobInstance`, `JobParameters`, `ExecutionContext`(Job)

<br>

## :pushpin: Chunk

```java
return stepBuilderFactory.get("chunkBaseStep")
  .<String, String>chunk(10)
  .reader(itemReader())
  .processor(itemProcessor())
  .writer(itemWriter())
.build();
```

- chunk step은 `ItemReader` -> `ItemProcessor` -> `ItemWriter` 순서로 진행
- reader에서 `null`을 return 할 때까지 Step 반복
- reader, processor는 item을 한 개씩 받아서 진행
- `<INPUT, OUTPUT>chunk([chunk 개수])`: INPUT, OUTPUT 제네릭 타입을 지정할 수 있다.
- `ItemReader` : `read()` -> return INPUT 타입
- `ItemProcessor` : `process(INPUT)` -> return OUTPUT 타입
- **`ItemWriter`** : `write(List<OUTPUT>)` -> 처리
  - List 인자의 크기는 `chunk([chunk 개수])` 여기서 결정
  - **reader, processor 거쳐서 데이터를 하나씩 받아서 List 인자에 담고 chunk 크기에 도달하면 일괄처리**
- tasklet 기반으로도 chunk 처럼 배치실행할 수 있으나 복잡하고 chunk 방식이 깔끔하다.

<br>

## :pushpin: JobParmeters

- JobParameters를 이용해서 기존의 chunkSize에 하드코딩으로 넣었던 값을 유연하게 변경 가능

### 첫 번째 방법

```java
StepExecution stepExecution = contribution.getStepExecution();
JobParameters jobParameters = stepExecution.getJobParameters();

String value = jobParameters.getString("chunkSize", "10");
int chunkSize = StringUtils.isNotEmpty(value) ? Integer.parseInt(value) : 10;
```

- StepExecution에서 JobParameters 객체를 가져옴
- 여기에 parameter로 지정된 내용을 가져온다.

```shell
-chunkSize=20 --job.name=chunkProcessingJob
```

- 실행시 `chunkSize` key 이름으로 옵션을 넣어준다.

### 두 번째 방법

- SpEL 사용 (**Sp**ring **E**xpression **L**anguage)
- `@Value` (springframework.beans) 사용
- `@JobScope` 어노테이션 있어야 jobParameters 인식

```java
@Bean
@JobScope
public Step chunkBaseStep(@Value("#{jobParameters[chunkSize]}") String chunkSize) {
    // 100개 데이터를 10개씩 chunk로 나눠서 실행
    return stepBuilderFactory.get("chunkBaseStep")
            .<String, String>chunk(StringUtils.isNotEmpty(chunkSize) ? Integer.parseInt(chunkSize) : 10)
            .reader(itemReader())
            .processor(itemProcessor())
            .writer(itemWriter())
            .build();
}
```
