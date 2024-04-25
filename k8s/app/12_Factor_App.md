# 12 Factor App

SaaS 애플리케이션을 잘 만들기 위한 원칙들을 모아 놓은 방법론  
뿐만 아니라 모던 백엔드 개발에 필요한 원칙들도 포함  
2010년 기점

Classical Backend Developement >> Modern Backend Development(SaaS)

- Modern Backend Development(SaaS)
  - Open Source Software
  - Cloud Environment

MSA, Cloud Native Programming

<br>

## Codebase

하나의 애플리케이션은 하나의 코드 베이스를 가져야 함
(한 애플리케이션은 하나의 Git Repository에서 관리되는 그런 개념)

Source Code >> Development, Test, Production

```
application.yaml
application-development.yaml
application-production.yaml
```