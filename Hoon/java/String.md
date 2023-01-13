# String Class

## String
String 클래스가 할 게 제일 많다.
🤔 왜 제일 많을까 ?

우리가 보통 프로그래밍 작업을 한다면 기본적으로 data를 조작한다던지..? 조작한 data에 대해서 화면에 출력할 수 있다. 그 외 여러가지도 많겠지만 말이다.

그 중에 Data Type 의 경우, 숫자형 int, double... / 문자 타입(char) / 문자열 (String) / 날짜 (Date)..

이 중 가장 많이 쓰게 되는 것이 String 클래스이다. 실제로도 많이 쓰이는데.. 클래스 중에서 _더하기 연산_을 제공하는 클래스는 String만이 존재한다.


>The String class represents character strings. All string literals in Java programs, such as "abc", are implemented as instances of this class.

String 클래스는 문자열을 나타낸다. "abc"와 같은 java 프로그램의 모든 문자열 _리터럴_은 이 클래스의 인스턴스로 구현된다.

>Strings are constant; their values cannot be changed after they are created. String buffers support mutable strings. Because String objects are immutable they can be shared.

문자열은 _상수_다. **생성된 후에는 해당 값을 변경할 수 없다.**
StringBuffer는 변경 가능한 문자열을 지원한다. String 개체는 변경할 수 없기 때문에 공유할 수 있다.

그렇다. String 클래스는 변경이 불가능(Immutable)하다. 한 번 생성된 것은 바꿀 수 없다. 예를 들어 두 문자를 더해서 나온 문자열은 아예 새로운 객체를 생성했다는 것이다.


***

왜 불변 클래스일까?
final이기에 변경할 수 없다.
```java
    private final byte[] value;
```
***


```java
	String a = "a"; // 0x100
	String b = "b"; // 0x200
	a = a + b; 	// 0x300
	System.out.print(a); // 출력 시 -> ab 
```

Java에서 String은 **원시타입(primitive type) 같이 보이지만 실은 참조형 클래스객체**이다. 위 코드에서도 마찬가지로 더하기 연산을 하고 있지 않은가? 그래서 원시타입처럼 쓰이지만 참조형 클래스라는 것이다.

원시(기본)타입은 **실제 데이터값을 저장하는 타입**이고, 참조 타입은 **객체의 주소를 저장하는 타입**이다.

때문에 서로의 값을 확인할 때 == 비교연산자보다 .equals() 메서드를 이용하는 것이 그 이유이다.

실제 객체는 **Heap 영역**에 저장되고, 참조 타입의 변수는 **Stack 영역**에 객체들의 주소가 저장되어서 객체를 사용할 때마다 그 객체의 주소를 불러와 사용하는 방식이다.

String은 원시타입 **같이** 보이지만 **참조형 클래스**라고 했다.
String클래스는 특별하게도 원시값 초기화(_리터럴_ ex -> int age = 27; // String에서는 쌍따옴표 (""))도 가능하고, new 연산자로 초기화도 가능하다.

```java
String test1 = "TEST"; // 리터럴
String test2 = "TEST";
String test3 = new String("TEST"); // new 연산 (객체)
String test4 = new String("TEST");
```

여러가지 방법으로 생성할 수 있다. 하지만 저 new 연산으로 생성하는 방법은 지양한다. 왜일까? 일단, 그 전에 자꾸 리터럴이 나오는데 리터럴이 무엇인지부터 알고 지양하는 이유에 대해 알아보자.
***
## 리터럴?
리터럴이란 데이터 그 자체를 의미한다. 변수에 넣는 **변하지 않는 데이터**를 의미한다. 위에서 봤듯이 예를 들면
```java
int a = 1;
```
이런 식이다. 앞에 final을 붙이면 a는 상수가 되며, 1은 리터럴이 된다.

데이터가 변하지 않도록 설계한 클래스를 불변 클래스라 한다. 이 클래스는 한 번 생성하면 객체 안의 데이터가 변하지 않으며 변할 상황이라면 아예 새로운 객체를 만들어 버린다.

그래서 String은 불변 클래스라고 말할 수 있다. 때문에 "ab"같은 문자열을 리터럴이라 부른다.

상수도 변하지 않는 값이 아니던가? 그러면 상수와 리터럴은 같은 뜻일까? 전혀 다르다. 이에 대한 내용은 [상수와 리터럴](https://mommoo.tistory.com/14)에서 확인해 보자.

ps) 🙇‍♂️ 해당 글 보고 쉽게 이해했습니다. 감사합니다 !!

***
## 리터럴("")과 String Constant Pool

📌String Constant Pool 이란 ? **Heap 영역 내부**에서 String 객체를 위해 별도로 관리하는 저장소이다.

new 연산자가 아닌 리터럴("")로 생성하게 되면 JVM은 String Constant Pool 영역으로 들어간다. test1과 test2는 리터럴로 초기화하였으며 같은 문자열이 공유할 수 있게 되어 메모리를 조금 더 효율적으로 쓸 수 있게 된다.

반면에, new 연산자를 이용하여 객체를 생성하게 되면 Heap 영역으로 들어가 같은 문자열이라도 서로 다른 주소값을 나타낸다. 그렇게 되면 메모리 공간이 비효율적이기에 이를 지양하는 것이다.

리터럴로 선언할 경우 String 클래스 내부에서는 어떻게 동작할까?

내부적으로 너무 복잡하기에 간단히 말하자면 intern() 메서드가 호출된다.
### intern()

String에서 값을 비교할 때 .equals()를 이용한다. == 비교연산자를 아예 쓰지 않는 것은 아니다. 리터럴로 생성된 객체와 new 연산자를 이용한 객체의 비교를 위할 때가 있다. 그럴 때 사용하는 게 intern이다.

```java 
String test1 = "TEST"; // 리터럴
String test2 = new String("TEST"); // new 연산 (객체)
String test3 = test2.intern(); // intern

System.out.println(test1 == test2); // false
System.out.println(test1 == test3); // true
```

앞에서 보았듯이 첫 번째 출력에서는 당연히 false 가 나올 것을 알 수 있다. 하지만 두 번째 출력은 true 값이 나온다.

test1처럼 리터럴은 pool에 해당 값이 있다면 그를 참조한다. new 연산으로 생성된 객체라 하더라도 intern() 메서드를 이용하면 서로가 같음을 표시한다.

== 비교연산자로 서로의 값을 비교하는 것이 equals()메서드보다 훨씬 빠르다. 하지만 intern() 메서드의 사용은 절대 지양해야한다.

왜일까?

새로운 문자열을 계속 만들어 내는 프로그램에서 intern() 메서드를 이용해 값들을 억지로 pool에 할당하도록 만들면, 영역 내에는 한계가 있기에 그 영역에 대해 별도로 메모리를 청소하는 단계를 거친다.

이 연산 하나 하겠다고 전체 시스템 성능을 저하시키는 일은 지양해야만 한다. 물론 그 문자열에 대해서만 사용한다면 큰 문제는 없겠지만 생성되는 문자열이 완전히 정해져 있는 것은 없기에 쓸 일은 없도록 해야한다.

String 클래스 내부에 있는 intern() 메서드의 설명과 코드를 가져왔다.
>문자열 개체에 대한 정식 표현을 반환합니다.
초기에 비어 있는 문자열 풀은 String 클래스에 의해 비공개로 유지됩니다.
intern 메서드가 호출될 때 풀이 equals(Object) 메서드에 의해 결정된 대로 이 String 개체와 동일한 문자열을 이미 포함하고 있으면 풀의 문자열이 반환됩니다. 그렇지 않으면 이 String 개체가 풀에 추가되고 이 String 개체에 대한 참조가 반환됩니다.
임의의 두 문자열 s 및 t 에 s.intern() == t.intern() 은 s.equals(t) 가 true 인 경우에만 true 입니다.
모든 리터럴 문자열과 문자열 값 상수 표현식은 인턴됩니다. 문자열 리터럴은 The Java™ Language Specification의 섹션 3.10.5에 정의되어 있습니다.
보고:
이 문자열과 내용이 같지만 고유한 문자열 풀에서 가져온 문자열입니다.
jls
3.10.5 문자열 리터럴

```java
public native String intern();
```

#### 📌 native ? Java가 아닌 다른 언어로 구현한 후 Java에서 사용하려 할 때 이용하는 키워드.

***

String은 변경할 수 없다고 했다. 예를 들어 "a"와 "b"를 **더해** "ab"로 변경하고 싶지만 이는 변경이 아닌 새로운 객체의 생성으로 본다. 하지만 변경이 가능한 String과 비슷한 클래스가 있다. 그것이 바로...

## StringBuffer
짧게 알아보자면..
>A thread-safe, mutable sequence of characters.

스레드로부터 안전하고 변경 가능한 문자 시퀀스이다.

🤔스레드로부터 안전하면 뭐가 좋을까..?
짧게나마 알아 본 결과로는...  일단 스레드 안전이란 ? 멀티 스레드 프로그래밍에서 어떤 함수나 변수, 혹은 객체가 여러 스레드로부터 동시 접근이 이루어져도 프로그램 실행에는 아무런 문제가 없음을 뜻한다.

음... 여러 스레드로부터 동시접근이 이루어져도 문제없음에 좋은 거 같다. 그래야 각 스레드에서의 함수 수행 결과가 잘 출력될 테니까.

>The principal operations on a StringBuffer are the append and insert methods, which are overloaded so as to accept data of any type. Each effectively converts a given datum to a string and then appends or inserts the characters of that string to the string buffer. The append method always adds these characters at the end of the buffer; the insert method adds the characters at a specified point.

주요 작업은 모든 유형의 데이터를 허용하도록 오버로드 되는 append, insert 메서드이다. 해당 문자열의 문자를 문자열 버퍼에 추가하거나 삽입한다. append 메서드는 항상 끝에 추가하며, insert 메서드는 지정된 지점에 문자를 추가한다.

>For example, if z refers to a string buffer object whose current contents are "start", then the method call z.append("le") would cause the string buffer to contain "startle", whereas z.insert(4, "le") would alter the string buffer to contain "starlet".

예를 들자면 "start"문자열에 "le"를 append 하면 "startle"가 된다.
"start"문자열에 insert(4, "le") 하게 되면 "starlet"가 된다. idx(4)에 지정된 문자("le")를 삽입하게 된다는 것이다.

>Every string buffer has a capacity. As long as the length of the character sequence contained in the string buffer does not exceed the capacity, it is not necessary to allocate a new internal buffer array. If the internal buffer overflows, it is automatically made larger.

모든 문자열 버퍼에는 용량이 있으며 문자열 길이가 용량을 초과하지 않는 한 새로운 내부 버퍼 배열을 할당할 필요가 없다. **오버플로된다면 자동으로 더 커진다.**

***

## String + operator


더하는(+) 연산을 지양해야한다고 하지만, 우선 문자열을 더하는 연산을 했을 때 어떻게 동작하는지 간단하게 알아보자. [+ operator for String in Java](https://stackoverflow.com/questions/2328483/operator-for-string-in-java)
에 있는 댓글을 인용하자면..

>Java 언어는 문자열 연결 연산자( + ) 및 다른 개체를 문자열로 변환하는 데 특별한 지원을 제공합니다. 문자열 연결은 StringBuilder(또는 StringBuffer) 클래스와 해당 append메서드를 통해 구현됩니다. 문자열 변환은 Object에 의해 정의되고 Java의 모든 클래스에서 상속되는 toString 메서드를 통해 구현됩니다. 문자열 연결 및 변환에 대한 추가 정보는 Gosling, Joy 및 Steele, The Java Language Specification 을 참조하십시오 .

라고 한다. (물론 String 내부에서 concat 메서드도 지원함 이 메서드도 더하기가 가능.) 내부적으로 StringBuilder or StringBuffer 클래스를 만든 후 다시 문자열로 돌려준다.

```java
String test1 = "a" + "b"; // ab
String test2 = new StringBuilder("a").append("b").toString(); // ab
```

이렇다. 지금은 간단하지만 수도없이 문자열이 합쳐지는 일이 빈번할 경우, 성능은 떨어질 것이며 메모리 효율성도 하락한다.

concat 메서드도 있는데 굳이 StringBuffer 또는 StringBuilder를 쓰는 이유는 무엇일까 ? concat은 왜 안 쓰이는지 내부적으로 살펴 보자. 설명과 함께 기재하겠다.
>지정된 문자열을 이 문자열의 끝에 연결합니다.
인수 문자열의 길이가 0 이면 이 String 객체가 반환됩니다. 그렇지 않으면 이 String 객체가 나타내는 문자 시퀀스와 인수 문자열이 나타내는 문자 시퀀스를 연결한 문자 시퀀스를 나타내는 String 객체가 반환됩니다.
예:
"cares".concat("s") returns "caress"
"to".concat("get").concat("her") returns "together"
매개변수:
str – 이 String 의 끝에 연결되는 String .
보고:
문자열 인수의 문자가 뒤따르는 이 개체의 문자 연결을 나타내는 문자열입니다.



```java
    public String concat(String str) {
        if (str.isEmpty()) {
            return this;
        }
        if (coder() == str.coder()) {
            byte[] val = this.value;
            byte[] oval = str.value;
            int len = val.length + oval.length;
            byte[] buf = Arrays.copyOf(val, len);
            System.arraycopy(oval, 0, buf, val.length, oval.length);
            return new String(buf, coder);
        }
        int len = length();
        int olen = str.length();
        byte[] buf = StringUTF16.newBytesFor(len + olen);
        getBytes(buf, 0, UTF16);
        str.getBytes(buf, len, UTF16);
        return new String(buf, UTF16);
    }
```
애초에 지나치는 코드들이 너무 많다. 이 메서드를 호출할 때마다 배열을 계속하여 재구성하는 과정을 거치는데 속도가 느려지는 건 당연하다.
StringBuilder나 StringBuffer는 크기를 먼저 지정해놓고 시작하기에(위에서 말했듯이 오버플로 되어도 자동으로 커져서 좋음) 더 빠르다.

하지만 단순하게 더하는 연산이라면 + 연산자를 채택하는 게 맞겠다.

[String은 Builder와 같지 않을까?](https://sundries-in-myidea.tistory.com/139)
추가... String + 연산과 StringBuilder(Buffer) 어차피 똑같은 거 아닐까? String +연산을 했을 때 어차피 Builder나 Buffer로 바뀌어서 연산이 될 텐데 말이다.

Ans) 그렇지 않다. Builder나 Buffer는 내부적으로 한 로직의 흐름으로 계속 이어지는 반면 String은 Builder를 생성하여 초기화하고 Append작업이 이루어져 toString까지 찍은 후 다시 Builder를 초기화하여 반복하는 방식이기에 훨씬 느리고 메모리양도 늘어날 뿐이다.

Builder의 로직 흐름은 [stackoverflow](https://stackoverflow.com/questions/46512888/how-is-string-concatenation-implemented-in-java-9)에서 쉽게 알아볼 수 있었다.
```java
        long start1 = System.currentTimeMillis();
        String test1 = "aa";
        for (int i = 0; i <1000000; i++) {
            test1= test1 + "aa";
        }
        long end1 = System.currentTimeMillis()-start1;
        System.out.println("String : " + end1);




        long start2 = System.currentTimeMillis();
        StringBuilder test2 = new StringBuilder("aa");
        for (int i = 0; i < 1000000; i++) {
            test2.append("aa");
        }
        long end2 = System.currentTimeMillis()-start2;
        System.out.println("StringBuilder : " + end2);




        long start3 = System.currentTimeMillis();
        StringBuffer test3 = new StringBuffer("aa");
        for (int i = 0; i < 1000000; i++) {
            test3.append("aa");
        }
        long end3 = System.currentTimeMillis()-start3;
        System.out.println("StringBuffer : " + end3);
        
        // 결과
        String : 137653
		StringBuilder : 15
		StringBuffer : 21
```

## 📌 Builder와 Buffer의 차이점? (중요)

서로 같지만 [차이점](https://velog.io/@heoseungyeon/StringBuilder%EC%99%80-StringBuffer%EB%8A%94-%EB%AC%B4%EC%8A%A8-%EC%B0%A8%EC%9D%B4%EA%B0%80-%EC%9E%88%EB%8A%94%EA%B0%80#2-stringbuilder-vs-stringbuffer) 하나가 있는데 그것이 바로 동기화(Synchronization)이다.

Builder는 동기화를 지원하지 않으며 속도면에서는 Buffer보다 빠르다. 그래서 위의 코드에서만 봐도 Builder가 가장 빨랐음을 알 수 있다. 동기화를 지원하지 않아서 단일 스레드에서 사용하는 것이 좋다.

Buffer는 동기화를 지원하여 멀티 스레드 환경에서도 안전하게 동작할 수 있다.
그래서 멀티 스레드 환경에서 문자열의 추가, 수정, 삭제 등이 빈번히 발생하는 경우에는 Buffer를 사용하는 것이 좋다.

쉽게 이해했습니다. 차이점을 기재해주신 분께 감사인사를 드립니다.🙇‍♂️

***
추가로 궁금했던 것...
String pool 과 Constant pool은 다른걸까? 같은걸까?
https://stackoverflow.com/questions/23252767/string-pool-vs-constant-pool 를 봐도.. 뭔가 이해가 안 된다.
