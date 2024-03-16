# ConfigMap & Secret

쿠베에서 설정을 저장하기 위해 사용하는 객체

- ConfigMap: 일반적으로 공개되어도 상관 없는 정보를 저장
- Secret: 외부에 공개되면 안 되는 설정값 저장
  - 실무에서는 SSL crt, key pem 파일에 대한 base64 변환된 문자열 저장

여러 파드에 걸쳐서 적용 가능

```shell
kubectl create secret generic my-db --from-literal=DB_PW=mypassword
```

`my-db` 라는 secret 객체를 생성  
위의 명령을 입력하면 실제 쿠베 secret에는 base64 encoding된 상태의 value가 저장된다. `DB=bXlwYXNzd29yZA==`  
해당 secret을 pod에서 사용하기 위해 꺼내면서 base64 decoding이 이루어진다.  

base64는 암호화가 아닌데? 그냥 단순 encoding 형식 중 하나일뿐  

Secret 객체는 그 값과 함께 etcd에 저장된다. etcd에 접근할 수 있다면 secret 객체에 저장된 내용을 확인해볼 수 있다.

<br>

## Secret과 보안

기본적인 명령어로는 Value를 보여주지 않음  
일상적인 kubectl 명령어로는 조회 불가능(물론 복잡한 방식으로 조회는 할 수 있음)

base64 인코딩이 되어 있기 때문에 값이 사고로 노출되어도 원문이 바로 유출되지 않음(잠깐 외부 화면에 노출된 경우 대비)  

설정을 통해 etcd에 저장할 때 암호화 해서 저장할 수 있음

접근 제어 설정을 통해 Secret에 접근할 수 있는 사용자와 프로세스를 제한할 수 있음

실제로 제대로 설정된 쿠베에서는 Secret을 쉽게 접근할 수 없도록 권한제어가 가능하긴 하다.

> 그렇다 해도 보안상 취약해보이긴 함...

<br>

## yaml 스펙 설정

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
    name: my-config
data:
    profile: "dev"
    db_host: "10.20.30.40"
    welcome-script: |
    echo Hello!
    echo DB is $db_host
    printenv
```

파이프라인(`|`) 기호를 통해 멀티라인 데이터도 표현 가능  

```yaml
apiVersion: v1
kind: Secret
metadata:
    name: my-secret
type: Opaque
data:
    DB_PW: bXlwYXNzd29yZA==
stringData:
    API_KEY: ABCD-1234-5678-ZYX
```

- `data`에는 base64 encoding된 문자열을 value로 넣어야하고,  
- `stringData`에는 평문으로 된 문자열을 넣어도 된다.
- `type`
  - `Opaque`: 일반적인 key, value 형태 설정값
  - Dockerconfig, Basic-Auth, TLS 등의 타입도 있음

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-config-app
spec:
    containers:
    - name: my-container
      image: my-app:1.0.0
      env:
      - name: SPRING_PROFILES_ACTIVE
        valueFrom:
            configMapKeyRef:
                name: my-config
                key: profile
    - name: my-secret
      valueFrom:
        secretKeyRef:
            name: db_password
            key: DB_PW
```

env 안에 valueFrom을 통해 ConfigMap, Secret 객체를 가져올 수 있다.

- name: 설정 객체의 이름
- key: 설정 객체 내에 정의된 설정의 키

환경변수와 다른 점은 여러 컨테이너에서 한꺼 번에 관리 가능, 유지보수 측면에서도 좋다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: my-image
    volumeMounts:
    - name: config-volume
      mountPath: /config-files
  volumes:
  - name: config-volume
    configMap:
      name: my-config
      items:
      - key: welcome-script
        path: "welcome.sh"
```
configMap 객체를 volume과 연결해서 사용 가능