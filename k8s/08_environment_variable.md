# Environment Variables

> 환경변수  
> 운영체제나 shell 단위로 설정할 수 있는 변수

**쿠베 입장에서 환경변수**: 스펙에 정적으로 정의하여 컨테이너 내부에 **주입 가능한 변수**  
이미지 자체가 이미 완성되어 있더라도 실행되는 파일에 환경변수를 주입할 수 있다.  

**개발자 입장에서 환경변수**: 애플리케이션 빌드, 배포 없이 변경 가능한 설정  
이미지를 다시 빌드, 배포할 필요없이 컨테이너만 재시작해주면 변경된 환경변수 반영 가능

**애플리케이션 입장에서 환경변수**: 가장 높은 우선순위를 가지며 가용성과 성능을 따지지 않아도 된다.  
config 서버와 통신을 통해 config 관련 변수들을 받아오는 경우 해당 서버에 문제가 있거나 통신장애가 발생하면 변수들을 가져오지 못하는 이슈 발생 가능. 하지만 환경변수는 정적으로 등록된 형태이기 때문에 이러한 걱정할 필요가 없다.

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: env-test-pod
spec:
    containers:
    - name: my-container
      image: busybox
      env:
        - name: SAMPLE_ENV
          value: "Sample Environment"
        - name: POD_NAME
          valueFrom:
            fieldRef:
                fieldPath: metadata.name
      command: ["sh", "-c", "sleep 1000"]
```
Pod의 container 레벨에서 설정, `env` 내부에 리스트 형태로 담을 수 있다.  
컨테이너 레벨의 스펙이기 때문에 다른 컨테이너들과 공유 X (같은 파드안에 속해있어도 공유 X)

일반적인 형태는 `name`, `value`  
`valueFrom`을 통해 일부 메타데이터를 환경 변수로 주입 가능(container의 image에 명시한 내용은 가져올 수 없음)

주입된 환경변수는 바로 사용 가능

yaml 스펙에 정의된 변수들을 컨테이너 내부에서 운영체제 환경변수 같이 바로 사용할 수 있도록 해준다.