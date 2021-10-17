# Kafka

- LinkedIn에서 자체 개발
- 지금은 오픈소스로 공개

## Topic

- DB의 테이블, 파일시스템 폴더와 유사한 성격
- producer는 kafka topic에 데이터를 전송하고 consumer는 topic의 데이터를 가져온다.
- topic은 `click_log`, `send_sms`, `location_log` 같이 데이터 성격에 따라 나눌 수 있다.

## Partition

- 하나의 토픽 안에 여러 파티션이 존재
- 컨슈머가 토픽 내부의 파티션에서 데이터(record)를 가져가도 데이터는 삭제되지 않는다.
- 다른 Consumer Group에서 맨처음 데이터부터 다시 가져올 수 있다.
  - Consumer Group이 달라야 함
  - `auto.offset.reset=earliest` 설정 필수
  - 이렇게 하면 동일 데이터를 두 번 처리할 수 있음(다양한 용도의 저장장치에 저장 가능)
- 파티션을 늘리는 것은 가능하지만 줄이는 것은 불가능(신중한 파티션 개수 설정 필요)
- `partition key`를 부여하면 해시값을 계산하여 특정 파티션에 데이터를 넣어준다.
- `partition key`가 `null`이면 round robin방식으로 파티션에 데이터를 넣는다.
- 파티션의 레코드 삭제는 다음으로 설정 가능
  - `log.retention.ms`: 최대 record 보존 시간
  - `log.retention.byte`: 최대 record 보존 크기(byte)

## Broker Replication

- `In-Sync-Replication(ISR)`
- kafka broker 장애에 대응하기 위한 방편
- 보통 3개 이상의 브로커를 설정하는 것이 좋다.
- `Leader Partition`: 원본 파티션
- `Follower Partition`: 원본에 의해 복제된 파티션
- `ISR`: `Leader` + `Follower`
- 브로커 하나가 장애가 나도 나머지 복제된 브로커가 대신 역할 수행 가능

## Consumer

(Dev원영 kafka 유툽 영상)
<img width="1337" alt="" src="https://user-images.githubusercontent.com/41675375/135961964-86949cdd-9a68-4167-b9f3-ce349528a31b.png">

- Consumer 한 개는 Topic의 partition 한 개에만 할당 가능
- partition은 다른 여러 컨슈머 그룹과 매핑관계를 가질 수 있다.
