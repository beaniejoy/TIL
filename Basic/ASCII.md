# ASCII

- `A`merican `S`tandard `C`ode for `I`nformation `I`nterchange
- 미국 정보 교환 표준 부호
- 영문 알파벳을 사용하는 대표적인 문자 인코딩
  - `인코딩`: 사용자가 입력한 문자나 기호들을 컴퓨터가 이용할 수 있는 신호로 만드는 것
  - 복잡한 신호를 `0`, `1`의 디지털 신호(2진수)로 변환


## 배경
- 1963년 미국 ANSI에서 표준화한 정보교환용 7비트 부호체계
- 8비트 컴퓨터에서도 활용되어 오늘날 문자 인코딩의 근간을 이룸

## 특징
- `000(0x00)`부터 `127(0x7F)`까지 총 128개 부호 사용
- 8비트 중 7비트만 사용(나머지 1비트)
  - 나머지 1비트는 통신 에러 검출을 위한 용도로 사용
  - `Parity Bit`: 7개 비트 중 1의 개수가 홀수면 1, 짝수면 0으로 Parity Bit를 붙임
  - 원시적인 CRC 체크섬 일종
- 영문 키보드로 입력할 수 있는 모든 기호들이 할당되어 있는 가장 기본적인 부호 체계
- 8비트 컴퓨터에서 기존 아스키 코드에 1비트를 더해 더많은 문자 표현
  - 아스키 코드에 없는 `코드 페이지`를 제정
  - 각 국의 언어에 따라 다양한 코드 페이지가 존재 대부분 아스키 코드에 기반해 제작
- 아스키는 2바이트 이상의 다양한 코드를 표현할 수 없었기에 현대에는 유니코드(Unicode)를 더 많이 사용
- 한글 인코딩은 2바이트 이상을 써야 가능(아스키 코드를 건드릴 수 밖에 없었음)
  - 초창기에 한글 깨짐이 심각했음(유니코드 제정 이후로도 깨짐은 연속, 멀티 바이트의 엔디안 문제 발생)
  - `UTF-8`(ASCII가 호환되는)이 제정되면서 깨짐 현상은 해결

> ASCII -> Unicode -> UTF8

## 대표적 문자
- 숫자 0: `0x30`, `48`
- 알파벳 a: `0x41`, `65` (~ `90` 소문자 알파벳)
- 알파벳 A: `0x61`, `97` (~ `122` 대문자 알파벳)