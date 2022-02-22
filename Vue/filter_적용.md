# Vue filter 기능

- vue에서 사용하는 filter 기능

<br>

## :pushpin: filter 정의

### 지역 컴포넌트에 직접 적용
```js
data() {
  return {
    amount: 200000
  }
},
filters: {
  currencyFormat(value) {
      if (value === null) {
          return 0
      }

      return value.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
  },
  monetaryUnitOfKorea(value) {
      return `${value} 원`;
  }
}
```
- Vue 객체 안에 filters라는 옵션을 추가할 수 있다.
- filters안에 function 형태로 정의하는데 인자로 받는 value는 filter 대상을 가리킨다.

### 전역 컴포넌트에 적용
```js
Vue.filter('currencyFormat', function(value) {
  if (value === null) {
      return 0
  }

  return value.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
});

Vue.filter('monetaryUnitOfKorea', function(value) {
  return `${value} 원`;
});
```
- 전역에 filter를 적용하고 싶을 때 사용

<br>

## :pushpin: 데이터에 filter 사용
```html
<div>{{ amount | currency | monetaryUnitOfKorea }}</div>
```
```
200,000 원
```
- 이런 식으로 filter chaining도 가능
- 맨 왼쪽 데이터 시작점 부터 차례대로 filter 과정을 거치게 된다.

```html
<input 
  type="text" 
  :value="amount | currencyFormat | monetaryUnitOfKorea" 
  disabled>
```
- `v-bind` directive를 이용해서 filter를 적용할 수도 있다.