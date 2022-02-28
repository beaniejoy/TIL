# javascript

자바스크립트는 함수 기반의 스크립트 언어이다. 함수적 프로그래밍 특성을 가진다.  

<br>

## 🔖 Statement
- 하나 혹은 여러 개의 표현식이 모여 문장을 이룬다.
- 모든 표현식은 문장이 될 수 있다.
- (보통) 문장의 끝에는 세미 콜론을 붙인다.
```javascript
true;
false;
29;
"Joy";
"Hello" + "JavaScript";
var name = "Beanie";
alert('Hello My World!!');
```
- 한 줄에 문장이 하나인 경우에는 세미 콜론을 붙이지 않아도 문제가 없다. **하지만, 관례적으로 붙인다.**
- 한 줄에 여러 문장을 적을 경우, 세미 콜론으로 문장을 구분해야 한다.
- 마지막 문장은 세미 콜론을 붙이지 않아도 문제가 없다.
- 마지막 문장의 결과가 반환된다.
```javascript
true; 26; 1000 + 900 + 400
// 2300을 반환
```
- if, for문도 문장이다.
- 이 경우에는 마지막 } 뒤에 세미콜론을 붙이지 않는다.
```javascript
if/for(statements) {

}
```
- 문장들이 모여 만들고자 하는 프로그램이 된다.

## 🔖 Keyword & Reserved Words
- 자바스크립트에서 특정한 목적을 위해 사용하는 단어
- 이러한 키워드들은 예악어(?)로 지정되어 있다.
```javascript
var name = 'Joy'; // var는 변수를 선언할 때 사용하는 키워드
```
### ▶ Reserved Words
```javascript
var return = '변수명'; // return은 예약어
function for() {} // for도 예약어
// 둘다 올바르지 못한 표현
```
### ▶ Future Reserved Words
- 앞으로 특정한 목적을 위해 사용할 가능성이 있어서 사용할 수 없는 예약어
- abstract, boolean, byte, char, class, const
debugger, double , enum, export, extends
final, float, goto, implements, import, int
interface, long, naive, package, private, protected
public, shor, static, super, synchronized, throws
transient, volatile

## 🔖 Identifier (식별자)
```javascript
var name = 'Mark'; // name
function hello() {} // hello
var person = {name:'Mark', age:37} // person, name, age 각각 식별자
```

- 대소문자를 구분한다.
```javascript
var myName = 'Mark';
var myname = 'Joy';
```

- '유니코드 문자', '$','_','숫자(0-9)'를 사용할 수는 있지만, 숫자로 시작할 순 없다.
- '예약어'는 사용할 수 없고, '공백 문자'도 사용할 수 없다.
```javascript
var name1;
var $name;
var _name;
// var 1name; 올바르지 않은 식별자
var 이름; // 가능은 하지만 영문을 사용한다.
```
- [mothereff.in/js-variables](https://mothereff.in/js-variables) : 올바른 식별자 구분해주는 사이트

## 🔖 Comment (주석)
- 소스 코드에서 프로그램에 영향을 주지 않고, 무시되는 부분
- 보통은 소스코드를 이해할 수 있도록 돕는 역할

```javascript
// 한줄 comment
/*
여러줄
주석
*/
```




