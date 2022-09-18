# **[Generic](https://techcourse.woowahan.com/s/NASxoOCp/ls/eie4W4ID)**

## 1. 제네릭이 뭐지?👀

JDK 1.5부터 도입되었다.

클래스 내부에서 사용할 데이터 타입을 외부에서 지정하는 기법이다. 컴파일 티임에 타입을 체크함으로써 코드의 안정성을 높여준다.

`List<T>` 속 **T** : 타입 매개변수
`List<String> strings = new ArrayList<String>();` 속 **String** : 매개변수화된 타입

### 타입 파라미터 컨벤션

![https://tecoble.techcourse.co.kr/static/b268c4f8f30a082779a188af838767eb/3e3fe/2020-11-09-generics-2.png](https://tecoble.techcourse.co.kr/static/b268c4f8f30a082779a188af838767eb/3e3fe/2020-11-09-generics-2.png)

## 2. 왜 쓰지?🧐

### 1. **컴파일 타임**에 강력한 타입 검사

런타임이 아닌, **컴파일 타임**에 강력한 타입 검사가 가능하다. **타입 안정성**을 제공한다.

**제네릭 미사용**

```java
List stringList = new ArrayList<>();
stringList.add("hunch");
stringList.add(1);
String result = 
    (String) stringList.get(0) * (String) stringList.get(1)
//-> 런타임 에러!!😂
```

**제네릭 사용**

```java
List<String> stringList = new ArrayList<>();
stringList.add("hunch");
stringList.add(1);
//-> 컴파일 에러!!😍
```

### 2. 캐스팅(타입 변환) 제거

만약 매번 캐스팅(타입 변환)을 하게 되면 프로그램 성능에 안 좋다.

제네릭을 쓰면 캐스팅을 할 필요가 없다. 코드도 간결해진다.

**제네릭 미사용**

```java
List stringList = new ArrayList<>();
stringList.add("hunch");
String result = (String) stringList.get(0);
//-> 매번 캐스팅이 필요함😂
```

**제네릭 사용**

```java
List<String> stringList = new ArrayList<>();
stringList.add("hunch");
String result = stringList.get(0);
//-> 캐스팅 안해도 됨!😍
```

## 3. 제네릭 타입과 메서드😄

어떻게 제네릭 타입을 선언하고 생성할까?

### 1. 클래스에서 생성

```java
class MyArray<T> {
    private T element;

    public MyArray(T element) {
        this.element = element;
    }

    public T get() { 
        return element; 
    }
}
```

**사용**

```java
MyArray<Integer> myArr = new MyArray<Integer>(1);

// Java SE 7부터 가능함.
MyArray<Integer> myArr2 = new MyArray<>(1);
```

### 2. 메소드에서 생성

```java
class MyArray<T> {
    private T element;

    public MyArray(T element) {
        this.element = element;
    }

    //...
    public<T> void printArray(T parameter) {
        System.out.println("클래스 필드에 정의된 타입: " + element.getClass().getName());
        System.out.println("메소드 필드에 정의된 타입: " + parameter.getClass().getName());
    }
}
```

**사용**

```java
MyArray<Integer> myArr = new MyArray<>();
myArr.printArray("파라미터");

// 클래스 필드에 정의된 타입: java.lang.Integer
// 메소드 필드에 정의된 타입: java.lang.String
```

## 4. 제네릭 변성🧊

`List<Object> objectList = new ArrayList<String>();`

→ 문법상 허용되지 않는다! 컴파일 오류!

> 변성을 제대로 이해하려면 "타입 S가 T의 하위 타입일 때, Box[S]가 Box[T]의 하위 타입인가?" 라는 질문에서 시작하는게 좋다.
>

### 변성(variance)이란?
 **타입의 계층 관계(Type Hierarchy)에서 서로 다른 타입 간에 어떤 관계가 있는지**를 나타내는 개념이다.

### 무공변(invariance)- `<T>`
타입 S가 T의 하위 타입일 때, Box[S]와 Box[T] 사이에 **상속 관계가 없는 것**

- java의 제네릭은 무공변이다!

### 공변(covariance) - `<? extends T>`

```java
// Number 배열에 하위 타입 값을 추가
Number[] numbers = new Number[3];
numbers[0] = new Integer(42);
numbers[1] = new Double(3.14);
numbers[2] = new Byte((byte) 0);

// Number 배열에 Interger 배열을 할당
Integer[] ints = { 1, 2, 3, 4};
Number[] nums = ints;
```

타입 S가 T의 **하위** 타입일 때, Box[S]가 Box[T]의 **하위** 타입인 것

- Java의 배열은 공변이다!

### 반공변(contravariance) - `<? super T>`

타입 S가 T의 **하위** 타입일 때, Box[S]가 Box[T]의 **상위** 타입인 것

## 5. 와일드 카드🃏

> 제네릭 객체를 메소드의 매개변수로 받을 때, 그 객체의 타입 변수를 제한하는 것!
>

```java
@Test
void genericTest() {
    List<Integer> list = Arrays.asList(1, 2, 3);
    printCollection(list);   // 컴파일 에러!!😂
}

void printCollection(Collection<Object> c) {
    for (Object e : c) {
        System.out.println(e);
    }
}
```

제네릭은 불공변🥲

- `Collection<Object>`는 `Collection<Integer>`의 하위타입이 아니므로 컴파일 에러가 발생한다.
- 오히려 제네릭 이전보다 실용성이 떨어진다…

이에 `와일드카드` 타입이 추가되었다!

### 1) Unbounded Wlidcards : `<?>`

모든 타입이 가능!

### 2) Upper Bounded Wlidcards : `<? extends MyArray>`

`MyArray`와 `MyArray`의 하위 타입!

- `<? extends Collection>`: `Collection`, `Map`, `List` …
- 상한 경계 : 해당 클래스/ 클래스의 자식 클래스
- → 공변 가능!

### 3) Lower Bounded Wlidcards : `<? super MyArray>`

`MyArray`와 `MyArray`의 상위 타입!

- `<? super Collection>` : `Object`, `Iterable`, `Collection` …
- 하한 경계 : 해당 클래스/ 클래스의 부모 클래스
- → 반공변 가능!

## 6. 와일드카드 제한

### 0) PECS

- `producer`(생성자) - `extends`(상한 제한)
- `consumer`(소비자) - `super`(하한 제한)

### 1) 상한(extends) 제한

```java
public void pop(Test<? extends String> myArray) {
    String element = myArray.get();
    //꺼내는 건 가능!

    element.set(new String());
    //저장은 불가!
}
```

- 생성하는 곳에서 상한 제한을 쓰자!

### 2) 하한(super) 제한

```java
public void push(Test<? super String> myArray) {
    element.set(new String());
    //저장은 가능!

    String element = myArray.get();
    //꺼내는 건 불가!
}
```

- 소비하는 곳에서 하한 제한을 쓰자!

## 제네릭 타입 소거⛔️

> 제네릭은 타입에 사용된 타입 정보를 **컴파일 타임에만 사용**하고 **런타임에는 소거**한다!
>

### 언바운드

```java
public class Test<T> {
    private T element;

    public Test(T element) {
        this.element = element;
    }

    public T get() {
        return element;
    }
}
//->컴파일 후!👀

coding@bagjiuui-MacBookAir main % javap -c Test

Compiled from "Test.java"
public class Test<T> {
  public Test(T);
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: aload_0
       5: aload_1
       6: putfield      #2                  // Field element:Ljava/lang/Object;
       9: return

  public T get();
    Code:
       0: aload_0
       1: getfield      #2                  // Field element:Ljava/lang/Object;
       4: areturn
//...
// 바이트 코드를 보면 Field element가 java/lang/Object로 설정되어있다.
```

공변(`extends`)

```java
public class ExtendsTest<T extends Number> {
    private T element;

    public ExtendsTest(T element) {
        this.element = element;
    }
}
//->컴파일 후!👀

coding@bagjiuui-MacBookAir main % javap -c ExtendsTest

Compiled from "ExtendsTest.java"
public class ExtendsTest<T extends java.lang.Number> {
  public ExtendsTest(T);
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: aload_0
       5: aload_1
       6: putfield      #2                  // Field element:Ljava/lang/Number;
       9: return
// 바이트 코드를 보면 Field element가 상한 제한인 java/lang/Number로 설정되어있다.
```

### 타입소거 과정

```java
public<T extends Number> void printArray(T parameter) {
    System.out.println("메소드 필드에 정의된 타입: " + parameter.getClass().getName());
}

//->컴파일러가 다음 방식으로 변경
public void printArray(Number parameter) {
    System.out.println("메소드 필드에 정의된 타입: " + (Number) parameter.getClass().getName());
}
```

1. 제네릭 타입 파라미터를 바운드나 `Object`로 변경해준다.
    1. 상한 제한 : 바운드
    2. 하한 제한/언바운드 : `Object`
2. 이후 타입이 일치하지 않을 경우 타입 캐스팅을 추가한다.
3. 확장된 제네릭 타입에서 다형성을 보존하기 위해 `bridege method`를 생성한다.

## 📚 참고자료

[[10분 테코톡] 그린론의 제네릭]([https://www.youtube.com/watch?v=w5AKXDBW1gQ](https://www.youtube.com/watch?v=w5AKXDBW1gQ))

[http://www.tcpschool](http://www.tcpschool.com/java/java_generic_concept)

[https://sungjk.github.io/2021/02/20/variance.html](https://sungjk.github.io/2021/02/20/variance.html)

[https://www.baeldung](https://www.baeldung.com/java-generics)

[https://jyami.tistory.com/99](https://jyami.tistory.com/99)
