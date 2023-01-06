# Reference

<br>

## :pushpin: call by reference?

```java
public class ArrayCopy {

    // copy and expand length from original array by for loop
    static void expandLengthByForLoop(int[] arr) {
        int[] newArr = new int[arr.length * 2];

        for (int i = 0; i < arr.length; i++) {
            newArr[i] = arr[i];
        }

        // 여기서 arr은 메소드 안에서의 parameter 변수값
        // 호출부에서 argument 넣은 arr의 참조값을 복사해서 가져오기만 하는꼴(사실 call by value)
        // 본래의 arr은 변하지 않는다.
        arr = newArr;
        System.out.println("method: " + arr);
    }

    public static void main(String[] args) {
        int[] arr = new int[5];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = i + 1;
        }

        System.out.println("[변경전]");
        System.out.println("arr.length: " + arr.length);
        for (int i = 0; i < arr.length; i++) {
            System.out.println("arr[" + i + "] : " + arr[i]);
        }

        System.out.println("before: " + arr);
        expandLengthByForLoop(arr);

        System.out.println("after: " + arr);

        System.out.println("[변경후]");
        System.out.println("arr.length: " + arr.length);
        for (int i = 0; i < arr.length; i++) {
            System.out.println("arr[" + i + "] : " + arr[i]);
        }
    }
}
```
```
before: [I@659e0bfd
method: [I@2a139a55
after: [I@659e0bfd
```
- `expandLengthByForLoop(arr)` 메소드에 파라미터로 arr 배열 참조값을 넣는 순간 해당 메소드 안에는 arr 참조값을 복사한 값이 들어가게 된다.  
- `arr = newArr`하는 것은 복사된 parameter 변수에다가 새로운 배열을 할당하는 꼴이되기 때문에 method 호출부의 본래의 arr은 원형 그대로 유지  
- 즉, 인자로 넣은 배열의 주소값을 복사해서 메소드 파라미터로 넘긴 것이므로 `call by reference` 보다 `call by value`가 맞다고 할 수 있다.