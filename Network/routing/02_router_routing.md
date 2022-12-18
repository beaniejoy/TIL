# Routing, Router

라우터에 대해서 알아보자  
라우터는 데이터가 최적의 경로를 찾아갈 수 있도록 해주는 장비  
최적의 경로는 **관리자에 의해 설정**된 routing table로 찾을 수 있고, **자동으로 설정된** 테이블에 의해서 찾을 수 있다.

- 서로 다른 네트워크를 연결하고 Broadcast Domain을 나눈다.
- packet이 목적지로 갈 수 있는 경로를 확인하고, 어느 경로가 가장 최적의 경로인지 결정
- Routing protocol에 따라 Routing table을 작성
  - `RIP`, `OSPF`, `EIGRP` 등이 존재

<br>

## :pushpin: 라우터 구성
- 단독형 라우터: 모든 인터페이스가 구성된
- 모듈형 라우터: 사용자 필요에 따라 확장 가능한
  - fast ethernet(LAN 선), serial ports(router), console port(입출력 장치 연결 지원) 등등
- IOS (Internetworking Operation System)
  - Cisco Router의 운영체제

### 하드웨어 구성요소
- **RAM**
  - IOS가 올라와 실행되는 곳
  - Routing table과 구성파일(설정내용)이 올라와 작동하는 곳
  - `Running-config` 설정내용을 저장(지금 메모리에서 실행중인 설정)
  - 당연히 휘발성 메모리
- **NVRAM** (Non Volatile RAM)
  - 비휘발성 메모리
  - `Startup-config` 설정내용을 저장(라우터가 Booting 될 때 올라오는 설정)
  - **Routing table은 저장하지 않는다.**(설정파일 내용만 저장)
- **Flash**
  - Flash Memory
  - 전원 차단해도 내용이 지워지지 않는 비휘발성
  - 주로 **IOS 이미지 파일 저장용도**로 사용됨
  - 이걸 통해서 IOS 버전 업만으로도 router 장비 교체없이 최신화 가능
  - NVRAM보다는 용량이 크다. (운영체제 저장공간이기 때문)
- **ROM**
  - Router의 가장 기본적인 내용 저장
  - 전원이 들어오는 경우 BIOS 같이 운영체제를 RAM에 올려주는 실행 코드가 저장되어 있음(bootstrap code)
  - 복구용 Mini IOS도 저장되어 있다.

<br>

## :pushpin: Router Booting Order
1. **Power on Self Test** (POST)
   - 전원이 들어왔을 때 자기 자신을 점검
2. **Load and run bootstrap code**
   - 일종의 BIOS 같은 코드 로그 및 실행
   - ROM에 저장되어 있음
   - bootstrap code에 의해 IOS를 찾고 RAM에 띄어서 실행시킨다.
3. **Find the IOS software**
   - IOS 운영체제는 Flash에 저장되어 있음
4. **Load the IOS software**
5. **Find the configuration**
   - NVRAM에 저장되어 있는 설정을 찾는다.
6. **Load the configuration**
   - NVRAM의 설정파일을 RAM에 로드 시킨다.
   - `startup-config` -> `running-config`로 옮겨짐
7. **Run**

> Router가 실행되다가 running-config 설정파일이 꼬이는 경우,  
> 다시 re-booting 하기 보다 startup-config를 다시 RAM에 load하면 된다.
```shell
> show startup-config
> show running-config 
> cp startup-config running-config
```

Cisco Router의 특이점이 있는데 router 명령어가 읽기 쉽게 되어 있다고 그들이 주장  
**설정파일 내용 자체가 명령어 모음집으로 되어있음**

> 4번째, 연결 케이블 종류와 라우터 여러가지 모드부터 진행
