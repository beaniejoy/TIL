# Entity와 DTO의 분리

## :pushpin: validation 문제

- Entity에 validation 기능까지 적용하지 않는 것이 좋다.
- Entity를 RequestBody로 받을 때 validation까지 한꺼번에 받는 것은 좋지 못하다.
- API 스펙마다 어떤 곳에서는 Order의 주문자이름이 필수입력값일 수도 있고 다른 곳에서는 필수값이 아닐 수도 있는데 하나의 Entity에 `@NotEmpty`로 설정되어 있으면 문제 발생

<br>

## :pushpin: Entity 스펙의 변경

- Entity 스펙이 변경되는 경우가 꽤 있다.
- Entity 스펙이 변경될 때 만약 API RequestBody로 Entity를 그대로 사용하고 있었다면 의도치않게 API 스펙까지 같이 변경될 수 있다.
- 해당 api를 사용하고 있는 프론트 입장에서는 갑자기 기능장애가 발생할 수 있는 것이다.

<br>

## :pushpin: Entity 스펙 노출

- 기본적으로 Entity 스펙은 외부에 노출되어서는 안된다.
  - entity는 실제 DB와 매핑되는 클래스이기 때문에 중요한 정보를 가지고 있다.
- api는 허용하는 스펙만 사용자에게 제공해줌으로써 기능으로써 api를 사용하게만 해야되지 Entity까지 전부 공개하면서 api 스펙을 제공할 필요는 전혀 없다.