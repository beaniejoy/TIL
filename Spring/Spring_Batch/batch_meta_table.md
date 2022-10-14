# Spring Batch 메타테이블

<br>

## :pushpin: Application 설정

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

<br>

## :pushpin: Spring Batch 관련 테이블 구조

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

## :pushpin: Job 실행

- **JobExecution**
  - JobExecution은 parameter가 같으나 다르나 항상 새롭게 생성
  - Job이 실행되면 `BATCH_JOB_EXECUTION` 테이블에 데이터 새로 생성됨
- **JobInstance**
  - 같은 parameter로 Job 실행시 이미 생성된 JobInstance가 실행  
    단 `RunIdIncrementer` 설정시 항상 새로운 JobInstance 실행
  - 다른 parameter로 Job 실행시 새로운 JobInstance 생성
