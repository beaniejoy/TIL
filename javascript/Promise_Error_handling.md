# Promise Error handling

<br>

## Promise catch

```javascript
function prom() {
  return new Promise((resolve, reject) => {
    reject(new Error('prom error!));
    // throw new Error('prom error!); 여기서 throw도 Promise에서 catch함
  })
  .catch((error) => {
    console.log(error);
  });
}
```

- `.catch`: Promise에서 발생한 모든 에러를 다룸
- `reject()` 혹은 `throw`를 통해 던져진 에러를 catch에서 처리
- 암시적 try, catch 문
  - Promise executer, handler 몸통 안에는 암시적인 try...catch 구문이 존재
  - 해당 구문 안에서 예외 발생시 예외를 잡아 reject 처럼 처리
  - 그래서 위에 reject, throw 둘다 Promise.catch에서 잡아낸 것임
  - reject, throw 뿐만 아니라 다른 에러도 catch 한다.

<br>

## Promise catch에 잡히지 않는 경우

```javascript
function prom(ms) {
  return new Promise((resolve, reject) => {
    // throw new Error('prom2 error');
    setTimeout(() => {
      reject(new Error('prom error!));
      // throw new Error('prom error!); 여기서 throw는 catch에서 잡지 못함
    }, ms);
  })
  .catch((error) => {
    console.log(error);
  });
}
```
- 여기서는 reject만 catch에서 잡아낸다.
- throw는 Promise executer가 진행되는 동안이 아닌 그 이후에 발생된 것이므로 catch를 못함
- 그래서 Promise executer 구문 안에는 에러 처리를 위해 `reject`만 사용할 것을 권장

<br>

## try...catch

```js
function prom(ms) {
  return new Promise((resolve, reject) => {
    // throw, reject 둘다 아래 try...catch 에서 잡힘
    // throw new Error('prom2 error');
    reject(new Error('prom2 error'));
  });
}

const func = async () => {
  console.log('func before await prom');

  try {
    await prom();
  } catch(error) {
    console.log('func catch :', error);
  }

  console.log('func after await prom');
}
```
- 여기서 Promise 안에서 발생한 에러에 대해 try...catch에서 잡아낸다.

```js
function prom(ms) {
  return new Promise((resolve, reject) => {
    // throw new Error('prom2 error');
    setTimeout(() => {
      reject(new Error('prom error!));
      // throw new Error('prom error!); 여기서 throw는 catch에서 잡지 못함
    }, ms);
  });
}

const func = async () => {
  console.log('func before await prom');

  try {
    await prom();
  } catch(error) {
    console.log('func catch :', error);
  }

  console.log('func after await prom');
}
```
- 위에서 Promise.catch에서 throw에 대해 잡아내지 못한 것처럼 try...catch에서도 잡아내지 못함

### reject 했을 때
```
func before await prom
func catch : Error: prom error!
    at Timeout._onTimeout (/Users/beanie.joy/Dev/2022/js-test/test2.js:7:14)
    at listOnTimeout (node:internal/timers:557:17)
    at processTimers (node:internal/timers:500:7)
func after await prom   ... func 마지막 부분 console에 나옴
```

### throw 했을 때
```
func before await prom
/Users/beanie.joy/Dev/2022/js-test/test2.js:8
      throw new Error('prom2 error');
      ^
Error: prom2 error
    at Timeout._onTimeout (/Users/beanie.joy/Dev/2022/js-test/test2.js:8:13)
```
- reject와 다르게 func 마지막 `console.log`가 찍히지 않는 것을 볼 수 있음
- **즉 catch가 제대로 이루어지지 않아서 그대로 전체 실행 종료가 되어버림**(Promise catch도 같음)

<br>

## try...catch, Promise catch 사용

- Promise catch로 error handling할 때 코드가 더 깔끔해진다.

```js
const func = async () => {
  console.log('func before await prom');

  try {
    await prom();
  } catch(error) {
    console.log('func catch :', error);
  }

  console.log('func after await prom');
}
```
- prom에 Promise catch를 지정하지 않으면 try...catch로 error를 handling해야 한다.
- 코드상 이러한 try...catch 부분이 조금 걸리적 거리는 경우가 있음
- Promise catch를 통해 Promise 단계에서 catch를 하면 굳이 try...catch를 하지 않아도 됨

```js
function prom(request) {
  return new Promise((resolve, reject) => {
    if (request === undefined)
      reject(new Error('prom error!));
    
    // 그 이후 로직 진행
    // resolve(...);
  })
  .catch((error) => {
    console.log(error);
  });
}

const func = async () => {
  console.log('func before await prom');

  // Promise 단계에서 catch를 한 상황
  const response = await prom();
  // response 가지고 이후 과정 진행하면 됨

  console.log('func after await prom');
}
```
- 이렇게 하면 prom을 사용하는 func 입장에서 try...catch 구문을 걷어낼 수 있어서 코드가 더 깔끔해짐
- func 입장에서는 error handling 처리까리 prom 내부에 캡슐화했기 때문에 에러 처리에 대해서 신경쓸 필요도 없고 prom과 같은 resolve 반환 데이터를 가지고 처리하는 로직에만 신경쓰면 됨