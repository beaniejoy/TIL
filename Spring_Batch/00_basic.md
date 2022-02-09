# Spring Batch Basic

- fastcampus 강의 필기내용
- [my github repo](https://github.com/beaniejoy/test-project-repository/tree/main/_courses/spring-batch-basic)

<br>

## 목차

- Job
- Step
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
