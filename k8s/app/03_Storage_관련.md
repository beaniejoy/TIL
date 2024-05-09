# Storage 관련

- Ephemeral Volume (임시 볼륨)
- Persistence Volume (영구 볼륨)

<br>

## 임시 볼륨

**주의할 점**
임시 볼륨 기준은 Pod, (컨테이너가 아니다.)
liveness probe에 의해 컨테이너가 재시작하는 경우 임시 볼륨은 그대로 유지될 수 있음
그래서 애플리케이션 기동할 때 임시 볼륨이 초기화가 안될 수 있다는 가정 하에 개발을 해야 한다.

다른 파드에서는 영향을 주지 않는다. 다만 하나의 파드에 multi container 구성이라면 컨테이너 간 영향을 줄 수 있다.

<br>

## 영구 볼륨

애플리케이션 자체에서 계속 유지되어야 하는 파일
여러 Pod들이 서로 공유해야 하는 저장 공간

AccessMode의 설정에 따라 성격이 달라짐
일반적으로 공유 스토리지의 성격을 가짐

최근에는 이러한 영구 저장 공간을 외부에 집중화된 공간에 저장하는 경우가 많아져서
쿠베의 영구 볼륨에 대한 수요는 줄어들고 있긴 함

- StatefulSet 활용한 구성

Pod에 고유한 스토리지 할당 가능
Pod마다 특정한 스토리지를 할당하고 싶을 때 StatefulSet 사용 가능

Deployment는 파드마다 특정을 지을 수 없지만 StatefulSet은 가능
(Database, Redis, Kafka 같은 경우에 StatefulSet 고려가능)

<br>

## k8s Storage 활용

사실 백엔드 관점에서 쿠베를 사용할 때 Storage를 되도록 사용하지 않는 것이 좋다.

k8s가 추구하고자 하는 방향은 애플리케이션 자체가 언제나 다운이 될 수 있는 상황을 가정해 즉시 복구 가능하도록 하는 것이다.
Pod의 생명주기가 짧다는 것을 인지해야 함 > 영속성을 가진 저장공간을 활용하기가 애매함

또한 Stateless한 애플리케이션 개발 관점에서 Storage 활용은 좀 안맞는 부분이 있음

- 저장 공간에 크게 의존성을 갖지 않는 애플리케이션 개발이 좋음
- 영구 볼륨이 필요하다면 외부 스토리지 서비스를 활용하는 구성이 좋음
  - aws 같은 클라우드 서비스에서 제공해주는 object storage를 활용하는 것이 좋다.
  - 내부적으로 별도의 storage service를 구성할 수 있음, 해당 서비스를 PV, StorageClass로 지정해서 영구볼륨 형태로 참조 가능
  - deployment에 연결해서 사용하기보다 별도 storage service 용 api server를 별도로 구성해서 StatefulSet으로 구성 <- 여기에 PV 연결, object storage 사용하듯이 구성할 수 있음
- 임시 볼륨 사용하는 경우 사용하는 저장공간의 크기에 유의해야 한다.
- 임시 볼륨 사용시 emptyDir 보다도 container 내부에 파일 저장하는 방식으로도 고려 가능

권한 같은 경우도
Pod 스펙에서 별도 권한 설정을 하지 않으면 애플리케이션에서는 루트 권한을 가지게 되어 권한 이슈는 따로 발생하지 않는다.

<br>

## k8s와 데이터베이스

데이터베이스는 보통 writable(source), Readonly(replica) set으로 구분이 된다.

**StatefulSet을 이용한 DB 구성**
각 파드 단위로 역할을 분배해 PV 연결

하지만 여기에는 문제가 있음

**1. source replica간에 failover 상황시 문제**

```
db-0.database-service.default.svc.cluster.local (source)
db-1.database-service.default.svc.cluster.local (replica)
```
이런 식으로 application에서 연결해서 사용하고 있는데
source에 문제가 발생해서 replica가 승격되면 애플리케이션에서는 db-1이 source가 되어버린다. 하지만 애플리케이션 단에서는 이를 감지 못한다.

**2. DB pod가 다른 노드에 실행될 때**

어떤 인스턴스가 장애가 발생하게 되어 pod가 다른 노드로 다시 배치 되었을 때 다른 PV와 연결될 수 있음

PV는 Node에 종속되지 않는 형태로 사용해야 한다.

> 결국 k8s cluster에 DB를 구성하지 않고 **외부 DB를 사용**
> 개발용 DB만 클러스터 내부에서 설치해서 사용해볼 순 있다.