# Array

## :pushpin: every

```js
function isBigEnough(element, index, array) {
  return element >= 10;
}
[12, 5, 8, 130, 44].every(isBigEnough);   // false
[12, 54, 18, 130, 44].every(isBigEnough); // true
```
- `every`: **배열에서 조건에 해당하지 않는 element를 만나면 false 반환하며 iteration 종료**
- 이걸 이용해서 forEach에서의 break를 구현할 수 있다.
```js
const rules = {
  required: (value) => value?.length > 0 ?? false,
  emailFormat: (value) => {
    const pattern =
      /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
    return pattern.test(value)
  },
}

const checkValidation = computed(() => {
  return Object.values(fields)
    .filter(input => route.name === 'SignUp' || input.isNeedLogin)
    .every(input => {
      return input.rules.every(rule => {
        if (!rules[rule](input.value)) {
          return false
        }

        return true
      })
    })
})
```
- 내부 조건 로직에서 false를 반환하면 break로 동작해 반복문을 즉시 종료