# Static

## Static 이란?
Static은 '정적인, 고정된'이라는 뜻을 가지고 있다.
이러한 이름을 가지는 이유는 바로 static이 앞에 붙는 변수나 메서드는
어떤 객체에 소속되는 것이 아닌, 클래스에 **고정되어 있는** 변수나 메서드이기 때문이다.


Java의 static은 클래스의 특정 인스턴스가 아닌 클래스에
속하는 클래스 변수와 메서드를 선언하는 데 사용된다.
즉, static 키워드는 클래스 수준의 데이터와 메서드를 정의하는 데 사용된다.

먼저 예시를 보기 전 static에 대해 이해해야할 핵심 사항과 특징을 짧게 서술한다.
***

### static 핵심사항
다음은 Java의 static에 대해 이해해야할 핵심 사항이다.

1. 클래스 변수: static 키워드로 선언되는 변수를 클래스 변수 또는 정적 변수라고 한다.
   클래스에 속하며 클래스의 특정 인스턴스에 속하지 않는다.
   즉, 클래스의 모든 인스턴스 간에 공유되는 정적 변수의 복사본이 하나만 있음을 의미한다.
2. 클래스 메서드: static 키워드로 선언하는 메서드를 클래스 메서드 또는 정적 메서드라고 한다.
   클래스의 **인스턴스를 만들지 않고** 호출할 수 있으며
   클래스 변수 및 기타 정적 메서드에만 액세스할 수 있다.
3. static 초기화 block : Java에서 정적 초기화 블록을 선언하여 클래스 변수를 초기화할 수도 있다.
   정적 초기화 블록은 클래스가 Java 가상 시스템에 의해 로드될 때 한 번만 실행된다.
4. static import : Java의 정적 가져오기 문을 사용하면 클래스의 정적 구성원을 가져와
   클래스 이름으로 한정하지 않고 코드에서 참조할 수 있다.


### 특징 ?
1. 메모리에 고정적으로 할당.
2. 인스턴스 생성 없이 바로 호출 가능
3. 프로그램이 시작과 동시에 메모리 static 영역에 적재 되며, 프로그램이 종료가 되면 동시에  해제된다.
4. static 메서드 안에서 인스턴스 변수 사용 불가

***


Java는 객체 지향 언어이며
기본적으로 작성하는 대부분의 코드에는 사용할 객체의 인스턴스가 필요하다.
```java
public class TesCt01 {
    public int testField;
    public void testMethod() {
        System.out.println("test method !");
    }
    public class TestClass {

    }
}
```
위의 testField, method, class를 사용하려면 인스턴스를 만들어 생성하고 사용할 수 있게 된다.
```java
public class testObject {
    public void doTest() {
        Test instance = new Test();
        instance.testField = 1; // 1로 초기화
        instance.testMethod(); // "test method !"
        TestClass class01 = instance.new TestClass();
    }
}
```
static으로 지정해놓고 나면 위의 예시와는 다르게 인스턴스를 만들지 않고 바로 사용이 가능하게 된다.

```java
public class Test02 {
    public static int testField;
    public static void testMethod() {
        System.out.println("test method !");
    }
    public static class TestClass { 
        
    }
}

public class testObject {
    public void doSomeStuff() {
        Test02.testField = 7;
        Test02.testMethod();
        Test02.TestClass blah = new Test02.TestClass();
        TestClass blah2 = new TestClass();
    }
}
```

static을 쓰는 데에 몇 가지 알아둬야할 것이 있다.

우선 첫 번째로는 해당 프로그램에서 하나의 정적 field값만이 있을 수 있다.

```java
public class Test03 {
    public void doTest() {
        //Test01 in non-static, Test02 in static
        Test01 instance01 = new Test01();
        Test02 instance02 = new Test02();

        // 5 -> 10 바뀌긴 하나 인스턴스를 생성하고...
       instance01.testField = 5;
       instance01.testField = 10; 
       
        // 생성 안 하고 바로 호출 가능, 5 -> 10 
        Test02.testField = 5;
        // testField 값이 재정의 된다.
        Test02.testField = 10;
    }
}
```

두 번째로 static 메서드와 내부 class 로 둘러싸여 있는 field에 접근할 수 없다.

```java
public class Test04 {
    int testField;
    public void testMethod() {
       System.out.println("test method !");
    }
   // 불가능
    public static void staticMethod() {
        testField = 3;
        testMethod();
    }
    
    public static class StaticClass {
        public void inStaticTestMethod() {
            // 불가능
            testField = 3;
            testMethod();
            
            // 가능 (허용)
            staticMethod();
        }
    }
   
}
```
또한 static은 내부 interface, enum도 가능하다.

위의 내용을 정리하자면 이렇다.해당 프로그램 내에서 하나의 정적 field값만이 있을 수 있고,
static 메서드, 내부 class 로 둘러싸여 있는 field에 접근할 수 없다. 그리고 static은 내부 interface, enum도 가능하다.

이 내용에 덧붙여서 몇 가지 더 추가한다.

첫 번째, 우리가 클래스를(프로그래밍) 설계할 때, 모든 인스턴스에 사용하는 것에 static을 붙인다.
인스턴스들을 생성하면, 서로 독립적이기에 서로 다른 값을 유지한다. 같은 값을 유지시키기 위해서는
static을 붙이는 것이 좋다.

두 번째, 위 예제에서도 보았듯이 static이 붙은 변수는 인스턴스를 생성하지 않아도 바로 호출이 가능하다.
static을 붙이면 클래스가 메모리에 (정적으로)할당될 때 이미 프로그램이 실행과 동시에 올라가 있기 때문이다.

***
+++ 그렇다면 어차피 프로그램을 실행할 것이고, 어차피 생성해서 사용할 일인데 모두 다 
static을 붙여서 사용하면 안 되는 것일까 ?

정답은 없지만 당연히 지양해야한다. 이유는 메모리 이슈인데, static을 사용하게 되면
프로그램을 시작할 때 메모리에 자동으로 할당된다고 두 세번 언급했다.
코드를 짜서 컴파일을 하게 되면 *메모리 영역 내(코드영역, 데이터 영역, 힙 영역, 스택 영역)
로 들어가는데, 여기서 *메모리 영역은...

1. 코드 영역 (정적) : 프로그램의 코드가 저장되는 영역. 함수 && 상수 컴파일에서 검사를 마치고 저장되어서 정적 메모리로 본다. 
2. 데이터 영역 (정적) : 전역변수와 **static** 변수가 저장되는 영역. 프로그램이 시작함과 동시에 할당되며 종료되면 동시에 사라진다. (*정적 메모리가 아니다. 전역 변수와 *정적 변수가 저장되는 영역임.)
3. 힙 영역 (동적) : 개발자의 필요에 의해 할당 및 소멸이 이루어지는 영역. 실행하게되면 그 크기는 늘어나거나 줄어들어 자유기억공간이라 부른다. 자유자재라서 동적할당으로 본다.
4. 스택 영역 (동적) : 지역, 매개변수가 저장되는 영역. (흔히 말하는 '변수'라고 말하는 것들을 선언하면 여기에 공간을 할당함.) 함수 호출이 끝나면 사라진다.

* 메모리에서 정적(Code,Data), 동적(Heap, Stack) 영역을 구분짓는 것과 변수의 동적,정적은 엄연히 별개다.
* 변수는 컴파일단계에서 메모리 할당을 받아 정적인데, 정작 할당받는 곳은 동적영역인 Stack 영역이다. 실행 중 공간의 크기를
조절하는 것은 불가능하지만 동적영역이기에 실행 중 언제든 해제할 수 있다.

그렇다면 static 은 데이터 영역으로 들어가 정적할당을 받는다. 그래서 왜 지양해야하는가?
할당받는 메모리의 크기가 정해져 있다. 때문에 실행할 때 수정이 불가능이라는 것. 결국 메모리의 낭비라는 이야기이다.
처음 필요한 공간은 100이었지만 나중에 10정도만 쓰고 싶다. 하지만 정적 할당 받았기에
10정도만 쓰고 싶어도 100을 사용해야하는 상황이 온다. 실행할 때 수정이 불가능하기 때문이다.

그럼 나머지 90의 메모리 공간은 아무것도 아닌 쓸모없는 텅 빈 공간이라는 것이다. 아예 고정이기에.. 150을 사용하고 싶어도
100 뿐이 고정이기에 50을 사용할 수 없을 것이다. 때문에 모든 곳에 static을 붙여주는 것은 좋지 못하다.

하지만 그렇다고 static을 무조건 쓰지 말라는 법은 아니다.
장점으로는 *메모리 누수를 걱정하지 않아도 된다는 것이다. 여기서 *메모리 누수란?

> 메모리 누수(memory leak) 현상은 컴퓨터 프로그램이 필요하지 않은 메모리를 계속 점유하고 있는 현상이다. 
> 할당된 메모리를 사용한 다음 반환하지 않는 것이 누적되면 메모리가 낭비된다. 즉, 더 이상 불필요한 메모리가 해제되지 않으면서 메모리 할당을 잘못 관리할 때 발생한다.
> ref 위키백과

크기는 고정이며 프로그램에서 그것을 알고 있기에 쉽게 기억공간을 사용할 수 있으며
어떠한 공간이 사라질 경우 OS에서 해당 공간은 사용하지 않는다라는 판단 하에 메모리를 회수한다.


***

세 번째, static이 붙어 있는 메서드에서 인스턴스 변수는 사용 불가이다. 위에서 봤듯이 static 메서드는 사용이 가능하지만
해당 메서드의 변수는 사용이 불가능한 모습을 볼 수 있었다. 인스턴스 변수는 인스턴스를 생성해야 사용할 수 있기에 그렇다.


네 번째, 만약 메서드에서 인스턴스 변수를 사용하지 않을 경우, static을 붙이는 것도 괜찮다. 
붙일 경우에는 메서드 호출 시간이 짧아지기 때문에 효율이 좋다. (static을 붙이면 
이미 할당되어 있기 때문에 찾는 시간이 짧음. 안 붙어 있다면 해당 메서드를 찾는 시간이 전자보다 길다.)

Q) static 메서드에서 인스턴스 변수를 사용할 수 있나 ?

-> 불가능. static은 실행과 동시에 할당되기에 불가능하다. 객체를 생성해야 인스턴스 변수를
사용할 수 있기에 먼저 메모리에 올라가버리면 불가능하다는 것이다.
하지만 반대로, 인스턴스 메서드 내에서 static 변수를 사용하는 것은 된다. 
변수가 먼저 올라가는 것이 아니니까.

다섯 번째, 새로운 클래스를 작성할 때 static을 어떻게 활용하면 좋을까 ? 어떤 경우에 사용하면 좋을지 ?
첫 번째의 내용처럼 먼저 멤버변수 중 모든 인스턴스에 같은 값을 유지하는 것이 있는지
보고 있다면 static을 붙이는 것이 좋다. 그리고 네 번째의 내용처럼 메서드 중 변수를 사용하지 않는 메서드에 대해 
static을 붙일 것인지 생각해본다.


static 공부를 하면서 정적 할당에 관해 알아보게 됐는데, 동적 할당도 같이 공부하면 좋을 것 같다.
이는 CS 메모리 공부하면서 추가적으로 공부할 예정이다. 해당 작성한 글 내에서는 동적 할당까지
작성하기에는 다소 주제가 벗어가는 느낌이라 넣지 않았다. 다음에 정적할당 vs 동적할당에 대해 
공부하면 될 것 같다.