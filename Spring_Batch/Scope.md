# Batch Scope

- `@Scope`은 bean의 생명주기(lifecycle)과 관련이 깊다.
- bean의 생성, 소멸 시점을 설정할 수 있음
- spring batch에는 두 가지 scope이 존재
  - `@JobScope`
    - job 실행 시점에 생성되고 job 종료 시점에 소멸
    - Step에 선언
  - `@StepScope`
    - Step 실행 시점에 생성 및 소멸
    - Tasklet, Chunk(ItemReader, ItemProcessor, ItemWriter)에 선언
- Job, Step 라이프사이클로 생성, 소멸을 진행하기 때문에 각 Job, Step에 대해 Thread Safe함
- `@Value("#{jobParameters[key]}")` 사용하려면 batch scope 선언 필수

<br>

## Scope 설정으로 인한 Thread Safe

- Scope를 통해 Thread Safe한 작업을 진행할 수 있음

```java
@Slf4j
@Component
public class TestJobTasklet implements Tasklet {
    private String name;
    private String value;

    @Override
    public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
        log.info(">>>>>>>>>>>>>>>>>> name: " + name);
        log.info(">>>>>>>>>>>>>>>>>> value: " + value);
        StepExecution stepExecution = contribution.getStepExecution();
        Long id = stepExecution.getId();

        updateInfo(id + " Task", id +" value");

        return RepeatStatus.FINISHED;
    }

    public void updateInfo(String name, String value) {
        this.name = name;
        this.value = value;
    }
}

// TestBatchConfig.java
@Bean
public Job testJob() {
    return jobBuilderFactory.get("testJob")
            .incrementer(new RunIdIncrementer())
            .start(testStep())  // 하나의 Tasklet으로 만들어진 Step을
            .next(testStep())   // 여러 개로 지정
            .build();
}

// testJobTasklet Injection 받음
@Bean
public Step testStep() {
    return stepBuilderFactory.get("testStep")
            .tasklet(testJobTasklet)
            .build();
}
```
- 예시로 하나의 Tasklet 객체를 Config에 주입해서 여러 Step에 적용하는 상황
- 위 코드를 실행하면 다음과 같이 로그가 나옴

```
>>>>>>>>>>>>>>>>>> name: null
>>>>>>>>>>>>>>>>>> value: null
...
>>>>>>>>>>>>>>>>>> name: 1 Task
>>>>>>>>>>>>>>>>>> value: 1 value
```
- 두 번째 Step 실행에서 `testJobTasklet` 객체의 값이 주어진 상태로 시작
- 결국 하나의 bean을 가지고 여러 Step에서 사용하고 있다는 것
- **Thread Unsafe**

```java
@Slf4j
@Component
@StepScope
public class TestJobTasklet implements Tasklet {
    //...
}
```
- `@StepScope`을 설정해주면 기존 Spring Bean Singleton 수행 방식에서 Batch 생명주기에 따라 bean 생성, 소멸 시점을 결정하게 된다.
- 즉 Spring Application에 따라 생성, 소멸되는 것이 아닌 Step의 시작과 종료에 따라 해당 bean의 생성, 소멸을 결정하게 된다는 것
- 그렇기 때문에 하나의 bean 객체를 가지고 여러 Step에서 사용해도 **Thread Safe**하다.