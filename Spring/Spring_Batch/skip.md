# batch skip 

```java
@Bean
@JobScope
public Step savePersonStep(@Value("#{jobParameters[allow_duplicate]}") String allowDuplicate) throws Exception {
    return stepBuilderFactory.get("savePersonStep")
        .<Person, Person>chunk(10)
        .reader(itemReader())
        .processor(itemProcessor(allowDuplicate))
        .writer(itemWriter())
        .listener(new SavePersonListener.SavePersonStepExecutionListener())
        .listener(new SavePersonListener.ChunkListener())
        .faultTolerant()
        .skip(NotFoundNameException.class)
        .skipLimit(2) // skip exception을 해당 횟수까지만 허용 (초과되면 step FAILED)
        .build();
}
```
`faultTolerant`를 선언해야 skip exception 관련 설정 가능  
`skip`에 skip 대상이 되는 exception을 설정
`skipLimit` skip 대상이 되는 exception 발생 가능 횟수 지정  
(예를 들어, limit 3이면 3번 발생까지는 step, job 모두 COMPLETED로 완료, 3번 초과 발생시 둘 다 FAILED 처리)
skipListener도 설정할 수 있는데 `listener()`를 `faultTolerant` 뒤에 설정해야 한다.