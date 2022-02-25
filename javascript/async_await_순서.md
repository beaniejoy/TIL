# async, await 사용시 순서보장

- `async`, `await`가 있는 function을 사용시 순서가 어떻게 되는지 테스트

<br>

## :pushpin: case 1.

- `prom`: `Promise` 객체 반환하는 함수
- `func`: `async`, `await`를 통해 `prom` 함수 호출
- `main`: 마찬가지로 `async`, `await` 적용, `func` 함수 호출
- 예상되는 기대: main 함수부터 호출한 순서대로 순차 진행

```js
function prom(ms) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log('prom function!'); // ...5
      resolve(ms);
    }, ms);
  });

  // return "hello";
}


const func = async () => {
  console.log('func before await prom'); // ...3

  await prom(1000); // ...4

  // prom에서 ms만큼 기다리고 after 실행
  console.log('func after await prom'); // ...6
}

const main = async () => {
  console.log('main before calling func'); // ...1

  await func(); // ...2

  console.log('main after calling func'); // ...7
}

main();
```

```
main before calling func
func before await prom
prom function!
func after await prom
main after calling func
```

<br>

## :pushpin: case 2.

- 제일 바깥쪽에 있는 main 함수에서 async, await을 사용하지 않는 경우

```js
const main = () => {
  console.log('main before calling func');

  func();

  console.log('main after calling func');
}
```
```
main before calling func
func before await prom
main after calling func   ... main method가 먼저 끝나게 됨
prom function!
func after await prom
```
- main 함수 내에서 func을 호출하는데 func의 내부로직이 async, await 비동기 처리를 했어도 main 함수 내에서 순서는 보장이 안됨


<br>

## :pushpin: case 3.

```js
function prom2() {
  return new Promise((resolve, reject) => {
    throw new Error('prom2 error!');
  }).catch((error) => {
    console.log('prom2 catch', error);  // ...3
  });
}

const func = () => {
  console.log('func before await prom');  // ...1

  prom2();

  console.log('func after await prom');   // ...2
}
```
```
func before await prom
func after await prom               ... 먼저 끝나게 됨
prom2 catch Error: prom2 error!
    at /Users/beanie.joy/Dev/2022/js-test/test2.js:18:11
    at new Promise (<anonymous>)
    at prom2 (/Users/beanie.joy/Dev/2022/js-test/test2.js:17:10)
    at func (/Users/beanie.joy/Dev/2022/js-test/test2.js:31:3)
    at Object.<anonymous> (/Users/beanie.joy/Dev/2022/js-test/test2.js:41:1)
```
- await 없이 실행하면 error catch한 부분이 마지막에 실행 된다.

```js
const func = async () => {
  console.log('func before await prom');  // ...1

  await prom2();  // ...2

  console.log('func after await prom');   // ...3
}
```
- 이 때는 순차적으로 실행