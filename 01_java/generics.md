# Generics

- 컴파일 시의 타입 체크를 해주는 기능(`compile-time type check`)
- 객체 타입 안정성 높이고 형변환 번거로운 작업 생략 가능

## 용어
```java
class Box<T> {}
```
- `Box<T>`: 제네릭 클래스. `T Box`, `T의 Box`
- `T`: `타입변수`, `타입 매개변수`(`타입 파라미터`)
- `Box`: `원시타입`(raw type)

```java
Box<String> b = new Box<String>();
```
- `제네릭 타입 호출`: 타입 매개변수에 타입을 지정하는 것 (`Box<T>` -> `Box<String>`)
- `매개변수화된 타입`(대입된 타입): 여기서 `String`

## 제한 사항

```java
class Box<T> {
    static T item; // error
    static int compare(T t1, T t2) {...} // error

    T[] itemArr; // T 타입 배열을 위한 참조변수는 OK
    T[] toArray() {
        T[] tmpArr = new T[itemArr.length]; // error. 제네릭 배열 생성 불가능
        //...
        return tmpArr;
    }
}
```
- static 맴버에 타입 변수 T를 사용할 수 없음
- `T`는 인스턴스 변수로 간주
- `new T[..];`: 컴파일 시점에 타입 T가 무엇인지 정확히 알아야 한다.(알 수 없음)

## Generics 객체 생성 및 사용

```java
public class Fruit {...}
public class Apple extends Fruit {...}

Box<Fruit> appleBox = new Box<Apple>(); // error
```
- `Box<Fruit>`, `Box<Apple>`
  - 두 클래스는 완전히 다른 타입이다.
  - Apple - Fruit 상속관계가 있어도 타입 매개변수에 선언된 것이면 완전 다른 타임이다.
```java
public class FruitBox<T> extends Box<T> {...}

Box<Apple> appleBox = new FruitBox<Apple>();    // OK
```
- 해당 구문은 가능하다.

```java
Box<Fruit> fruitBox = new Box<>();
fruitBox.add(new Grape());  // OK
fruitBox.add(new Apple());  // OK. void add(Fruit item)
```
- `add` 메소드의 파라미터가 Fruit 이므로 해당 상속받은 클래스의 객체를 원소로 넣을 수 있다.

## Generic 클래스의 제한
```java
class FruitBox<T extends Fruit> {
    List<T> list = new ArrayList<>();
}

class FruitBox<T extends Fruit & Eatable> {...}
```
- 제네릭 클래스의 타입 파라미터를 직접 제한할 수 있다.
- `extends`를 사용해 특정 클래스를 상속받은 클래스 타입만으로 제한할 수 있다.
- interface를 구현한 특정 클래스 타입으로 제한하고 싶을 때도 `extends`로 사용(`implements` X)
- `&`로 묶을 수 있다.

## 와일드 카드

```java
class Juicer {
    static Juice makeJuice(FruitBox<Fruit> box) {
        String tmp = "";
        for(Fruit f : box.getList()) tmp += f + " ";
        return new Juice(tmp);
    }
}
```
- 이렇게 메소드 파라미터 타입을 설정하면 문제가 발생할 수 있다.

```java
FruitBox<Fruit> fruitBox = new FruitBox<>();
FruitBox<Apple> appleBox = new FruitBox<>();

Juicer.makeJuice(fruitBox); // OK
// Juicer.makeJuice(appleBox); // error
```
- `FruitBox<Fruit>`, `FruitBox<Apple>` 두 개는 별개의 타입이다.  
  (그냥 완전히 다른 타입이라 생각하면 됨, **코틀린에서 이런 개념이 아주아주 중요**)
- 하나의 메소드에서 다형성처럼 타입 파라미터도 받게 하기 위해서 `와일드 카드`를 사용하면 된다.

```java
class Juicer {
    static Juice makeJuice(FruitBox<? extends Fruit> box) {...}
}
```
- 이런 식으로 하면 타입 파라미터도 다형성처럼 사용할 수 있다.