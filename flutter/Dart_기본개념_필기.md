# Dart 기본 개념 필기

## 변수 및 타입

```dart
bool isTrue;
isTrue = true;
```
이렇게 먼저 선언하고 나중에 값 할당 가능

```dart
Null thisIsNull = null;
```
dart 언어에는 Null 형이 따로 존재(신기)

```dart
var value = 1;
value = 'is Error?';
//Error: A value of type 'String' can't be assigned to a variable of type 'int'.
```
kotlin 처럼 var 형이 존재  
재할당이 가능하긴한데 처음 할당 했던 타입의 값을 넣어야 한다.

```dart
dynamic value = 1;
value = 'Hello!!'; // OK!
```
최초 할당한 값의 타입과 다른 타입의 값을 재할당해도 에러발생 X

### Null Safety 타입
Dart 2.12 버전부터 도입

```dart
// Nullable Type
int? double? bool? String?
```
kotlin과 유사해 보임

```dart
// Non-nullable Type
int!, double!, bool!, String!
```

<br>

## Class

```dart
// default constructor
// named constructor
class Point {
  double x;
  double y;

  Point(this.x, this.y);
}

void main() {
  Point point = Point(1, 2);
}
```

<br>

## 기본적인 문법 중 특이사항

switch case 문에 break;가 사라졌다. (3.0 버전 이후부터)

```dart
int num = 101;

switch (num) {
  case 1:
    print('Switch $num');
  case 2:
    print('Switch $num');
  case 3:
    print('Switch $num');
  case 4:
    print('Switch $num');
  // Only Dart >= 3.0
  case > 100:
    print('Switch $num');
  default:
    print('invalid num');
}
```
중간중간 break; 문이 없어도 해당하는 case 구문만 실행  
case 조건에 식을 넣어도 가능(`case > 100`)

```dart
List<int> indices = [0, 1, 2, 3, 4, 5];
for (final index in indices) {
  print('Running for index $index');
}
```
`final`로 원소 지정

```dart
try {
  print(10 ~/ 0); // 나눈 몫을 구하는 수식어
} catch (error, stack) {
  print(error);
  print(stack);
} finally {
  print('finally end');
}

try {
  print(10 ~/ 0); // 나눈 몫을 구하는 수식어
} on UnsupportedError catch (error, stack) {
  print('UnsupportedError');
} on TypeError catch (error, stack) {
  print('TypeError');
} catch (e, s) {
  print('other errors');
} finally {
  print('finally end');
}

try {
  throw Exception('Unknown Error');
} on UnsupportedError catch (error, stack) {
  print('UnsupportedError');
} on TypeError catch (error, stack) {
  print('TypeError');
} catch (e, s) {
  print('other errors');
  rethrow; // 다시 해당 error로 던지게 됨
} finally {
  print('finally end');
}
```
catch 문에 error와 stacktrace 모두 가져올 수 있음  

<br>

## 동기 & 비동기 처리

- async / await / Future > 1회만 응답을 돌려받는 경우

```dart

Future<void> todo(int sec) async {
  await Future.delayed(Duration(seconds: sec));
  print('TODO Done in $sec seconds');
}

void main() {
  // 아래 세 개의 메소드는 비동기로 호출되어서 1, 3, 5 이렇게 완료됨
  todo(3);
  todo(1);
  todo(5);
}
```

- async* / yield / Stream > 지속적으로 응답을 돌려받는 경우

```dart
Stream<int> todo() async* {
  int counter = 0;

  while(counter <= 10) {
    counter++;
    await Future.delayed(Duration(seconds: 1));
    print('TODO is Running $counter');
    yield counter; // 수시로 return 객체(Stream<int>)에 값을 전달해주는 기능
  }

  print('TODO is Done');
}

todo().listen((event) {});
```