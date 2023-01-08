## 제네릭이란 ?

자바에서 제네릭(generic)이란 데이터의 타입(data type)을 일반화한다(generalize) 는 것을 의미한다.

제네릭은 클래스나 메서드에서 사용할 내부 데이터 타입을 컴파일 시에 미리 지정하는 방법이다.

이렇게 컴파일 시에 미리 타입 검사(type check)를 수행하면 다음과 같은 장점이 있다.

1. 클래스나 메서드 내부에서 사용되는 객체의 타입 안정성을 높일 수 있다.
2. 반환값에 대한 타입 변환을 할 때 instanceof 같은 타입 검사를 줄일 수 있다.

ex)

```java
class MyClass<T> { 
    T value;

    T getValue() {
        return this.value;
    }

    void SetValue(T value) {
        this.value = value;
    }
}
    /**
     * MyClass 내부에서 String으로 타입을 지정해두었기 때문에 별도의 타입 검사 없이 안전하게 이용할 수 있다.
     * */
    public static void main(String[] args) {
        MyClass<String> myClass = new MyClass<>(); //컴파일시에 미리 String으로 지정
        myClass.SetValue("test");
        //타입 검사 없이 자동으로 String으로 변환되어서 리턴된다.
        String test = myClass.getValue();
    }

```

 
## Class Literals 이란
 - 클래스 리터럴이란 클래스, 인터페이스, 배열 타입들의 이름 or primitive types 에 `.class` 토큰이 표현법이다.
 - `클래스이름.class`의 형태의 표현법을 Class Literals 라고한다.
 - Class Literals로 해당 클래스에 관련된 메타 정보를 얻을수있다.

### 쉽게 생각하기
 - `Class<Integer> a = Integer.class` <- 여기서  `Integer.class` 는 Class<Integer> 타입의 값이다.
 - `String a = "test"`  <- 여기서 test는 String타입의  값이다.
 - Integer.class는 Class type의 객체라고 볼수있는것이다. 다만 리터럴이기때문에 상수 값이다.

## Type Token이란?
- 타입 토큰(Type Token)은 쉽게 말해 타입을 나타내는 토큰이며, 클래스 리터럴이 타입 토큰으로서 사용된다.
- 타입의 안전성이 필요할때 사용한다.
- 어떤 값을 보내거나 받을때 내가의도한 Type은 이것이다 라고 명시적으로 적어주는 것이라고 생각하면된다.

ex)
```java
// String.class 클래스 리터럴을 타입토큰 파라미터로 사용하는 예제    
    public static <T> T makeType(Class<T> clazz /*타입 토큰*/ ) throws InstantiationException, IllegalAccessException {
        return clazz.newInstance();
    }

    public static void main(String[] args) throws InstantiationException, IllegalAccessException {
        String test = makeType(String.class/*리터럴*/);
    }
    
```


## Generic Erasure
제네릭 소거자를 말한다.
java는 내부적으로 제네릭을 컴파일해서 class 파일로 만들 때 제네릭을 제거하고 가장 상위 타입으로 변경한다.

- ex)
  `<T> -> Object` - unbounded type

- `<T extends Number> -> Number` -bounded type



- MyClass 바이트 코드

```java

class generic/MyClass {

  // compiled from: MyClass.java

  // access flags 0x0
  // signature TT;
  // declaration: value extends T
  Ljava/lang/Object; value    // -> value type이 Object로 변경된 것을 볼 수 있다.

  // access flags 0x0
  <init>()V
   L0
    LINENUMBER 3 L0
    ALOAD 0
    INVOKESPECIAL java/lang/Object.<init> ()V
    RETURN
   L1
    LOCALVARIABLE this Lgeneric/MyClass; L0 L1 0
    // signature Lgeneric/MyClass<TT;>;
    // declaration: this extends generic.MyClass<T>
    MAXSTACK = 1
    MAXLOCALS = 1

  // access flags 0x0
  // signature ()TT;
  // declaration: T getValue()
  getValue()Ljava/lang/Object;  // -> return type이 Object로 변경된 것을 볼 수 있다.
   L0
    LINENUMBER 7 L0
    ALOAD 0
    GETFIELD generic/MyClass.value : Ljava/lang/Object;
    ARETURN
   L1
    LOCALVARIABLE this Lgeneric/MyClass; L0 L1 0
    // signature Lgeneric/MyClass<TT;>;
    // declaration: this extends generic.MyClass<T>
    MAXSTACK = 1
    MAXLOCALS = 1

  // access flags 0x0
  // signature (TT;)V
  // declaration: void SetValue(T)
  SetValue(Ljava/lang/Object;)V   // -> parameter type이 Object로 변경된 것을 볼 수 있다.
   L0
    LINENUMBER 11 L0
    ALOAD 0
    ALOAD 1
    PUTFIELD generic/MyClass.value : Ljava/lang/Object;
   L1
    LINENUMBER 12 L1
    RETURN
   L2
    LOCALVARIABLE this Lgeneric/MyClass; L0 L2 0
    // signature Lgeneric/MyClass<TT;>;
    // declaration: this extends generic.MyClass<T>
    LOCALVARIABLE value Ljava/lang/Object; L0 L2 1
    // signature TT;
    // declaration: value extends T
    MAXSTACK = 2
    MAXLOCALS = 2
}
```


## 그래서 문제점이 뭐야??
- 리플렉션을 이용해야 하거나 `List<String>` , `Map<String,Object>` 와 같이 중첩된 타입을 파라미터나 변수로 사용해야 할 때 문제가 생긴다.
- 리플렉션은 런타임 시점에서 Class의 정보를 읽어야 하는데 Generic의 경우 타입 정보를 소거하기 때문에 해당 제네릭 타입을 읽어올 수 없다.
- `List<String>.class` 와 같이 사용 할 수없다.

ex) 
```java
  public static void main(String[] args) throws NoSuchFieldException {
        MyClass<String> a = new MyClass<>();
        Class<?> type = a.getClass().getDeclaredField("value").getType();
        System.out.println(type); //class java.lang.Object
        test(List<String>.class); // -> 컴파일 오류
    }
    /**
     * 컴파일 시점에서 타입을 소거해버리고 Object로 변경하기
     * 때문에 원하는 String이 찍히지 않고 Object가 찍히는 것을 알 수 있다.
     * */
    public static <T> T test(Class<T> a){
        ...    
    }
```
## SuperTypeToken
Neal Gafter 이 고안한 방법으로 익명 클래스를 이용해서 컴파일 타임에 원하는 타입 토큰을 저장해두는 방식으로(상속 구문의 제네릭 타입은 소거되지 않는다.)
제네릭 타입을 실제 Type 변수로 저장해 두기 때문에 타입이 소거되지 않는 특징이 있다.

ex)
```java

public class SuperTypeToken<T> {
    final Type type;   // 제네릭 T를 실제 Type으로 내부에 저장해둔다. 따라서 소거되지않음

    protected SuperTypeToken() {//생성자를 이용해서  T(리터럴을) -> Type변수에 저장하는 작업
        Type stype = this.getClass().getGenericSuperclass();
        System.out.println("type = " + stype);
        if (stype instanceof ParameterizedType) { 
            this.type = ((ParameterizedType) stype).getActualTypeArguments()[0];
        } else {
            throw new IllegalArgumentException("잘못된 타입 정보");
        }
    }

    public Type getType() {
        return type;
    }
}
  

public static void main(String[] args) {
    SuperTypeToken<List<Integer>> integer = new SuperTypeToken<List<Integer>>() {}; 
    /**
     * 해당코드는 익명함수를 사용한 것이기 때문에
     * class Sup extends SuperTypeToken<List<Integer>> {
     * }
     * SuperTypeToken<List<Integer>> integer = new Sup();
     * 이것과 같다.
     * 제네릭은 SuperTypeToken의 <T>는 소거해서 Object로 만들어버리지만
     * extends SuperTypeToken<List<Integer>> 처럼 상속 구문의 제네릭 부분은 소거하지 않기때문에 접근이 가능하다.
     * */
    System.out.println("integer.getType() = " + integer.getType());
    
    /**
     * type = generic.SuperTypeToken<java.util.List<java.lang.Integer>>
     * integer.getType() = java.util.List<java.lang.Integer>
     * */
  }
```


## 참고
>https://www.youtube.com/watch?v=01sdXvZSjcI
