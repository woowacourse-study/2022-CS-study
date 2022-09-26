# 제네릭

## 들어가기에 앞서..

## 제네릭이란?

제네릭은 다양한 타입의 객체들을 다루는 메서드나 컬렉션 클래스의 컴파일 시의 타입 체크를 해주는 기능을 말한다. 객체의 타입을 컴파일 시에 체크하기 때문에 객체의 타입 안정성을 높이고 형변환의 번거로움이 줄어든다. 이를 좀 더 풀어서 설명하면, 의도하지 않은 타입의 객체가 저장되는 것을 막고, 형변환의 오류를 제거할 수 있다고 할 수 있다.

### 제네릭 클래스의 선언

다음과 같이 Box라고 정의된 클래스가 있다고 가정하자.

```java
class Box { 
    Object item;
		
    void setItem(Object item) {
        this.item = item;
    }
		
    Object getItem() {
        return item;
    }
}
```

해당 클래스를 제네릭으로 변경하면 다음과 같이 변경할 수 있다. 다음과 같이 클래스 옆에 <T>를 붙이면 된다. 그리고 Object를 모두 T로 변경한다.

```java
class Box<T> {
    T item;
		
    void setItem(T item) {
        this.item = item;
    }

    T getItem() {
        return item;
    }
}
```

`Box<T>`에서 T를 **타입 변수**라고 한다. 만약 타입 변수가 여러 개인 경우에는 `Map<K, V>`와 같이 콤마를 구분자로 사용하면 된다. 여기서 K는 Key(키)를 의미하고 V는 Value(값)을 의미한다.

기존에는 다양한 종류의 타입을 다루는 메서드의 매개변수나 리턴타입으로 `Object` 타입의 참조변수를 많이 사용하여서 타입 캐스팅이 불가피했지만, 이제는 `Object` 타입 대신 원하는 타입을 지정하기만 하면 되는 것이다.

다시 돌아와서, 제네릭 클래스 `Box`의 객체를 생성할 때는 다음과 같이 참조변수와 생성자에 타입 T 대신에 사용될 실제 타입을 지정해주어야 한다.

```java
Box<String> b = new Box<String>();
b.setItem("ABC");

String item = b.getItem();
```

만약 제네릭을 사용하지 않았다면, 다음과 같이 사고가 발생했을 가능성도 있다.

```java
Box b = new Box();
b.setItem("ABC");
b.setItem(new Object());

b.getItem(); // 예외 발생
```

### 제네릭 용어

```java
class Box<T> {} 
```

- Box<T> 제네릭 클래스, ‘T의 Box’ 또는 ‘T Box’라고 읽는다.
- T 타입 변수 또는 타입 매개변수
- Box 원시 타입

컴파일 후에 Box<String>과 Box<Integer>는 이들의 ‘원시 타입’인 Box로 변경된다. 즉, 제네릭 타입이 제거되는데 이를 **제네릭 타입 제거**라고 한다.

### 제네릭 제한

제네릭 클래스 Box의 객체를 생성할 때, 객체별로 다른 타입을 지정하는 것을 적절한 방법이다.

```java
Box<Apple> appleBox = new Box<Apple>();
Box<Grape> grapeBox = new Box<Grape>();
```

그러나 모든 객체에 대해 동일하게 동작해야하는 static 멤버에 타입 변수 T를 사용할 수는 없다. T는 인스턴스 변수로 간주되기 때문이다.

```java
class Box<T> {
		static T item; // 에러
}
```

또한 제네릭 타입의 배열을 생성하는 것도 허용되지 않는다. 제네릭 배열 타입의 참조변수를 선언하는 것은 가능하지만, new T[10]과 같이 배열을 생성하는 것은 불가능하다.

```java
class Box<T> {
		T[] itemArr; // 제네릭 배열 타입의 참조변수 가능

		T[] toArray() {
				T[] tmpArr = new T[itemArr.length]; // 에러
		}
}
```

이를 생성할 수 없는 것은 new 연산자 때문인데, new 연산자는 컴파일 시점에 타입 T가 뭔지 정확하게 알아야 한다. 만약 위의 코드 처럼 비슷하게 작성을 하고 싶다면, `Refelection API`의 `newInstance()`를 사용해서 동적으로 객체 배열을 생성하거나, `Object` 배열을 생성해서 복사한 다음에 `T[ ]`로 형변환하는 방법 등을 사용하자.

### 제네릭 클래스의 객체 생성과 사용

Box<T> 객체를 한번 사용해본다고 가정해보자. 객체 생성시 참조변수와 생성자에 대입된 타입이 일치해야 한다. 일치하지 않으면 예외가 발생한다.

```java
Box<Apple> appleBox = new Box<Apple>();
Box<Apple> appleBox = new Box<Grape>(); // 에러
```

이는 두 타입이 상속관계에 있어도 마찬가지이다. Apple이 Fruit의 자손이라고 가정하고 다음 코드를 작성해도 예외가 발생한다.

```java
Box<Fruit> appleBox = new Box<Apple>(); // 에러
```

단, 두 제네릭 클래스의 타입이 상속관계에 있고, 대입된 타입이 같으면 객체를 생성할 수 있다.

```java
Box<Apple> appleBox = new FruitBox<Apple>();
```

Box<T> 객체에 객체를 추가한다고 가정해보자. 만약 대입된 타입과 다른 타입의 객체를 추가하려고 한다면 예외가 발생할 것이다.

```java
Box<Apple> appleBox = new Box<Apple>();
appleBox.add(new Grape()); // 에러
```

하지만 타입 T가 Fruit인 경우, 즉 부모인 경우 Fruit의 자식들은 해당 메서드의 매개변수가 될 수 있다.

```java
Box<Fruit> fruitBox = new Box<Fruit>();
fruitBox.add(new Fruit());
fruitBox.add(new Apple());
```

## 와일드 카드

와일드 카드는 어떤 상황에서 사용되어야 할까?? 다음을 예시로 들 수 있다.

매개변수에 과일박스를 대입하면 주스를 만들아서 반환하는 Juicer라는 클래스가 있고, 과일을 주스로 만들어서 반환하는 makeJuice() 라는 static 메서드가 있다고 가정하자.

```java
class Juicer {
    static Juice makeJuice(FruitBox<Fruit> box) {
        //.. 생략
    }
}
```

Juicer 클래스는 제네릭 클래스가 아닌데다, 제네릭 클래스라고 해도 static 메서드에는 타입 매개변수 T를 매개변수로서 사용할 수 없으므로, 제네릭 적용이 불가능하다.

```java
FruitBox<Fruit> fruitBox = new FruitBox<Fruit>();
FruitBox<Apple> appleBox = new FruitBox<Apple>();

Juicer.makeJuice(fruitBox);
Juicer.makeJuice(appleBox); // 에러
```

위의 코드처럼 FruitBox<Fruit>로 고정을 할 경우, FruitBox<Apple> 타입의 객체는 makeJuice의 매개변수가 될 수 없기 때문에 사용이 불가능하다. 그럼 오버로딩을 하면 괜찮지 않을까라는 생각을 할 수도 있다.

```java
class Juicer {
    static Juice makeJuice(FruitBox<Fruit> box) {
        //.. 생략
    }

    static Juice makeJuice(FruitBox<Apple> box) {
        //.. 생략
    }
}
```

하지만 오버로딩 역시 불가능하다. 제네릭 타입이 다른 것만으로는 오버로딩의 규칙이 성립하지 않기 때문이다. 제네릭 타입은 컴파일러가 컴파일할 때만 사용하고 제거를 하기 때문에, **메서드를 중복 정의**한 것과 같은 효과를 가지게 된다. 그럼 어떻게 해결할 수 있을까?? 이런 상황에서 **와일드 카드**를 사용해볼 수 있다.

```java
class Fruit<T extends Fruit> extends Box<T> {}
```

- <? extends T> 와일드 카드의 상한 제한, T와 그 자손들만 가능
- <? super T> 와일드 카드의 하한 제한, T와 그 조상들만 가능
- <?> 제한 없음. <? extedns Object>와 동일

아까의 문제 코드를 한번 해결해보자. 와일드 카드를 이용해서 makeJuice()의 매개변수 타입을 FruitBox<? extends Fruit>로 바꾸면 문제의 코드를 해결할 수 있다.

```java
class Juicer {
    static Juice makeJuice(FruitBox<? extends Fruit> Box) {
        //.. 생략
    }
}

FruitBox<Fruit> fruitBox = new FruitBox<Fruit>();
FruitBox<Apple> appleBox = new FruitBox<Apple>();

Juicer.makeJuice(fruitBox);
Juicer.makeJuice(appleBox);
```

그럼 하한 제한은 어떻게 사용하는 것일까?? 역시 다음 예제를 통해서 확인해보자.

```java
static <T> void sort(List<T> list, Comparator<T> c)
```

만약 여기서 T에 Apple이 대입된다면 다음과 같이 코드가 바뀌게 된다.

```java
static void sort(List<Apple> list, Comparator<Apple> c)

class AppleComp implements Comparator<Apple> {
    //.. 생략
}
```

그런데 Grape에 대한 정렬도 같이 되어야 한다고 한다. 이런 경우, 위 처럼 중복되는 코드를 하나 더 만들어야 하게 된다.

```java
class GrapeComp implements Comparator<Grape> {
    //.. 생략
}
```

이런 경우, 하한 제한을 사용하게 되면 문제를 해결할 수 있다. **Apple과 Grape의 조상은 Fruit라고 가정하고 비교대상이 Fruit에 존재**한다면, 하한 제한을 통해 문제를 해결할 수 있다.

```java
class FruitComp implements Comparator<Fruit> {
    //.. 생략
}
```

## 제네릭 메서드

메서드의 선언부에 제네릭 타입이 선언된 메서드를 제네릭 메서드라고 한다. 제네릭 타입의 선언 위치는 반환 타입의 바로 앞에 위치한다.

```java
static <T> void sort(List<T> list, Comparator<? super T> c)
```

주의할 점으로, 제네릭 클래스에 정의된 타입 매개변수와 제네릭 메서드에 정의된 타입 매개변수는 전혀 별개의 것이라는 점이다. 같은 타입 문자 T를 사용해도 다른 것이라는 것을 기억해야 한다.

추가적으로 제네릭에서는 static 메서드에서도 제네릭 타입 선언이 가능하다. 그 이유는 메서드에 선언된 제네릭 타입은 지역 변수를 선언한 것과 같다고 할 수 있기 때문이다. 이로인해, 메서드가 static이든 아니든 큰 상관이 없다.

```java
static <T extends Fruit> Juice makeJuice(FruitBox<T> box) {}
```

제네릭 메서드를 활용하면 매개변수의 타입이 복잡할 때 유용하게 사용이 가능하다. 만약 다음과 같은 코드가 있다면 타입을 별도로 선언함으로써 코드를 간단하게 만들수가 있다.

```java
// 변경 전
public static void printAll(ArrayList<? extends Product> list1,
                            ArrayList<? extends Product> list2) {
    // .. 생략
}

// 변경 후
public static <T extends Product> printAll(ArrayList<T> list1,
                                           ArrayList<T> list2) {
    // .. 생략
}
```

조금 더 복잡한 코드를 통해서 확인해보자.

```java
public static <T extends Comparable<? super T>> void sort(List<T> list)
```

해당 메서드는 어떤 의미를 가지고 있을까??

1. 타입 T를 요소로 하는 List를 매개변수로 허용한다.
2. T는 Comparable을 구현한 클래스여야 하며, T 또는 그 조상의 타입을 비교하는 Comparable 이여야 한다는 한다는 것을 의미한다.

만약 T가 Student이고, Person이 자손이라면, <? super T>는 Student, Person, Object가 가능하다.

## 제네릭 타입의 제거

컴파일러는 제네릭 타입을 이용해서 소스파일을 체크하고, 필요한 곳에 형변환을 넣어준다. 그리고 제네릭 타입을 제거한다. 이렇게 하는 이유는 제네릭이 도입되기 전인 1.5 JDK 이전과의 호환성을 유지하기 위해서이다.

기본적으로 제네릭 타입이 제거되는 과정은 다음과 같다.

1. 제네릭 타입의 경계를 제거한다.

   제네릭 타입이 <T extends Fruit> 일 경우, T는 Fruit로 치환된다. <T> 일 경우, T는 Object로 치환된다. 그리고 선언한 부분은 제거된다.

2. 제네릭 타입을 제거한 후에 타입이 일치하지 않으면, 형변환을 추가한다.

   List 같은 경우 메서드인 get()은 Object 타입을 반환하므로 형변환이 필요하다.

3. 와일드 카드가 포함되어 있는 경우에는 다음과 같이 적절한 타입으로의 형변환이 추가된다.
