# Volume & Ephemeral Volume

## Volume

쿠베에서 사용하는 저장소

파일을 저장할 수 있다면 volume으로 만들 수 있다. 네트워크 스토리지, 클라우드 서버 스토리지 등등

volume은 말 그대로 저장소 개념  
container 내부에 있는 종속적인 개념으로 이해할 수 있는데 다른 개념.

독립적인 운영체제 시스템(가상)을 갖추고 있다. 컨테이너와 구분되는 개념

### Volume Mount

Volume을 원하는 컨테이너와 연결하는 것  
ex. usb 드라이브를 pc에 꽂으면 운영체제가 인식하게 되어 그 안에 있는 내용 접근 가능

실제로 volume을 container에서 사용하기 위해 연결 시키는 작업이 필요 (리눅스의 mount 개념과 유사해보임)

container의 특정 디렉토리를 volume과 연결시킴. 마치 icloud 연결하면 전용 디렉토리가 생겨서 그 안에서 해당 cloud stoarge 내용을 접근할 수 있는 것처럼

```yaml
# Pod spec
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
        path: welcome.sh
```
주의해야 할 점은 Pod안에 여러 container를 지정할 수 있고, 여러 volume도 지정할 수 있다. 즉 container와 volume은 별개의 존재, container의 하위 스펙이 아니다. (서로 N:N 관계)

- `volumes`
  - my-config라는 configMap 객체를 가져오겠다.
  - 그 안에 있는 내용 중 key가 welcome-script인 내용을 가져와서 welcome.sh 파일로 만들겠다.
- `volumeMounts`
  - pod의 volumes에 정의한 name을 가지고 매핑함(같은 이름으로 연결해야함)
  - `mountPath`: container 내부에서 어떤 디렉토리에다가 해당 volume과 연결할 것인지

```yaml
spec:
  containers:
    - name: my-container
      image: my-image
      volumeMounts:
      - name: upload-volume
        mountPath: /tmp/uploads
  volumes:
  - name: upload-volume
    emptyDir: {}
```
volume에 빈 디렉토리를 설정해서 그거를 컨테이너에 mount 시키는 구조로 사용 가능  
**Pod가 종료되면 해당 emptyDir도 전부 날라가게 된다.**  

특정 디렉토리를 메모리를 사용하도록 설정가능  

어떤 앱에서 사용자가 파일을 업로드할 때 서버가 받아서 파일로 저장하고 이걸 다시 object storage에 저장  
영속화가 마무리 되었을 때 임시로 저장한 파일은 날려도 무관  
다운로드할 때도 마찬가지  

위와 같은 케이스에 대해서 `emptyDir`가 적합하다고 할 수 있다.

<br>

## Ephemeral Volume

Pod와 라이프사이클을 동일하게 가져가는 볼륨  
임시 볼륨

emptyDir, hostPath, configMap, secret 등이 있음

hostPath는 파드가 삭제되어도 볼륨안에 있는 내용은 지워지지 않아서 같은 노드에 다시 파드가 떴을 때 해당 내용을 다시 참조하게 된다. 좋아보이지만 다른 노드와 일관성이 없어서 잘 사용 X  
`PersistenceVolume`을 사용하면 된다.

configMap, secret을 통해 만들어진 볼륨은 일종의 복사본이라 해당 볼륨에 수정, 삭제가 이루어져도 원본 configMap, secret에는 영향을 주지 않는다.

volumeMounts 할 때 readOnly 옵션을 줄 수 있다.

Pod 스펙 내에서 인라인으로 정의된다. Pod 내에서 volume 만들어 바로 mount 하는 방식으로 정의한다.  
왜냐하면 Pod와 Ephemeral Volume은 같은 주기를 가지기 때문

일반적으로 `emptyDir`만 사용해서 기능을 구현하는 것이 유리하다

## References

- [[Kubernetes] Volume - Deep Dive (EmptyDir, HostPath, Network Volume)](https://anggeum.tistory.com/entry/Kubernetes-Volume-Deep-Dive)