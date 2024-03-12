# Job & CronJob

pod를 설정할 때 `restartPolicy: OnFailure`를 통해 한 번만 성공처리되도록 해서 배치를 실행할 수 있다. 하지만 이러한 파드들은 완료되고 나서 수동으로 삭제처리해야 한다. (번거로운 과정)

쿠베 자체에서 이러한 배치 작업을 위한 객체를 제공해줌

## Job

어떤 작업을 처리하고 종료된 Pod를 관리하는 객체

```yaml
apiVersion: batch/v1
kind: Job
metadata:
    name: my-simple-job
spec:
    activeDeadlineSeconds: 60
    ttlSecondsAfterFinished: 20
    parallelism: 1
    completions: 3
    backoffLimit: 4
    template:
        spec:
            containers:
            - name: my-container
              image: busybox
              command: ["sh", "-c", "sleep 10"]
            restartPolicy: OnFailure
```
- `parallelism`: batch용 pod 실행시 병렬로 몇개까지 처리할 것인지
- `completions`: 배치 실행을 위해 총 몇개의 파드를 이용할 것인지
  - parallelism: 2, completions: 5 상황에서 2개, 2개, 1개 이런식으로 처리될 것임
  - 하지만 배치 작업시 병렬로 처리한다는 것은 많은 리스크를 내포하고 있음, 중복 처리 가능성, 데이터에 대한 락 걸릴가능성 등등 고려해야할 요소들이 많음
  - 그래서 병렬로 처리하는 경우는 많지 않음
- `restartPolicy`: `OnFailure`, `Never` 둘 중 하나 취급
  - `OnFailure`: container가 정상 종료 되지 않은 경우 container 자체를 재시작시킴(파드는 같은 파드)
  - `Never`: backoffLimit이 설정된 상황에서 파드 자체를 죽이고 다시 새로운 파드 띄워서 실행, 노드 포지션 자체도 변경될 가능성 존재
  - `backoffLimit`(default 6): 몇 번까지 재시작을 수행할 것인가(파드든, 컨테이너든)
  - 배치 실패한 경우 그냥 실패한 채로 내버려두는 경우를 위해 **backoffLimit을 1**로 설정하면 될듯
- `activeDeadlineSeconds`: 작업을 생성하고 끝날 때까지의 시간, 이후로 넘어가면 timeout
- `ttlSecondsAfterFinished`: 실행된 잡 객체가 성공, 실패 결과 이후 다시 실행될 여지가 없는 경우 종료된 작업을 정리해주는 시간(설정하는 걸 추천, 안하면 수동으로 정리해주어야 한다.)

<br>

## CronJob

스케줄에 따라 반복적으로 실행할 Pod를 관리하는 Job

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
    name: my-cronjob
spec:
    startingDeadlineSeconds: 200
    concurrencyPolicy: Forbid
    schedule: "0 * * * *"
    # jobTemplate 아래는 Job 객체의 스펙과 유사
    jobTemplate:
        spec:
            template:
                spec:
                    containers:
                    - name: my-container
                      image: busybox
                      command: ["echo", "sleep 10"]
                    restartPolicy: OnFailure
```
- `concurrencyPolicy`: 설정한 schedule 주기에 따라 job 실행되어야 하는데 이전 작업이 끝나지 않은 경우 어떻게 할 것인지
  - `Allow`(default): 이미 실행중인 작업 있어도 새 작업 실행
  - `Forbid`: 이미 실행중인 작업이 끝나고 나서 새 작업 실행
  - `Replace`: 이미 실행중인 작업이 있으면 강제 종료하고 새 작업 실행
- `startingDeadlineSeconds`
  - 지정한 시간동안 CronJob 시작까지 기다리게 해준다.
  - 만약 이전에 수행한 작업이 있었고 Forbid 정책이면 언제까지 기다리는지, 노드에 자원이 부족해 여유가 생길 때까지 기다려야하는 상황에서 언제까지 기다리는지 등의 상황을 위해 설정
- 위에서 CronJob이 결국 실패한 경우 그대로 종료되는 것이 아니라 다음 schedule 도래되었을 때 다시 실행됨

batch-pod를 띄워서 실행시킬 때 기존 service용도의 pod가 실행되고 있는 노드 중 여유가 있는 곳에 배치되어 실행된다는 점에서 매력적이다. (별도 batch용 node를 구성할 필요가 없음, 물론 필요에 의해 구성도 가능)

하지만 위와 같이 돌아가게 하려면 배치 자체가 분산처리가 가능하도록 개발을 해야한다는 점이 있다. kube 상에서 돌아갈 배치프로그램과 다른 곳에서 돌아가는 배치프로그램과 성격이 달라질 수 있다. 그래서 Job, CronJob은 그렇게 인기 있는 객체는 아니다.