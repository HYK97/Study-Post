* Garbage Collection의 개념
  프로그램을 개발하다 보면 유효하지 않은 메모리(Garbage)가 발생한다. C언어에서는 malloc(), calloc() 함수를 통해 메모리를 동적 할당하고, free() 함수를 통해 메모리를 해제한다. 하지만 Java나 Kotlin에서는 프로그래머가 메모리를 직접 해제하지 않는다. 그 이유는 JVM의 Garbage Collector가 불필요한 메모리를 알아서 정리해주기 때문이다. Garbage Collection은 메모리 관리 기법 중 하나로, 프로그램이 동적 할당했던 메모리 영역 중에서 필요없게 된 영역을 해제하는 기능이다. 더이상 사용할 수 없게 된 영역이란, 어떤 변수도 가리키지 않게 된 영역을 의미한다. 예시를 통해 Garbage Collection을 이해할 수 있다.



* Garbage Collection의 대상이 되는 객체

객체들은 실질적으로 힙 영역에 생성되고, 메서드 영역, 스택 영역에서는 힙 영역의 생성된 객체들을 참조하는 형식으로 구성된다. 하지만 메서드 호출이 완료되어 힙 영역의 객체의 메모리 주소를 가지고 있는 참조 변수가 삭제되는 현상이 발생하면 위 그림의 빨간색의 객체처럼 참조되지 않은 객체가 발생하게 된다. 해당 객체는 가비지 컬렉션의 대상이 된다.

* Garbage Collection 예제

```java
class Person {
private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

public class PersonTest {
    public static void main(String[] args) {
        Person person = new Person();
        person.setName("NaverMan");
        person = null;

        // Garbage 발생
        person = new Person();
        person.setName("KakaoMan");
    }
}
```

위의 예제를 보면, person 객체를 생성한 후, setter 메서드로 name을 "NaverMan"으로 설정해주었다. 그 후 person에 null을 대입하여 person은 더이상 "NaverMan"의 객체를 가리키지 않고 null을 참조하게 되었다. 그 후 객체를 다시 생성하면 "NaverMan"에 해당하는 객체는 참조가 되지 않고 사용이 되지 않아 유효하지 않은 메모리(Garbage)가 된다. Java나 Kotlin에서는 이러한 메모리의 누수를 방지하기 위하여 Garbage Collector가 주기적으로 검사하여 메모리를 청소해준다.



* Garbage Collection의 구조

힙 영역은 Eden, Survival, Old generation으로 나뉜다. Eden, Survival로 구성되어 있는 Young generation 영역에 해당하는 Garbage Collection을 Minor GC라고 부른다. 그에 비해 Old generation으로 구성되어 있는 Garbage Collection을 Major GC 또는 Full GC라고 부른다.



* Garbage Collection의 동작 원리 - Mark and Sweep 알고리즘

Mark And Sweep 알고리즘은 가비지 컬렉션이 동작하는 원리로 루트에서부터 해당 객체에 접근 가능한지에 대한 여부를 메모리 해제의 기준으로 삼는다. Mark And Sweep은 위의 그림과 같이 총 3가지 과정으로 나뉘게 된다.

Mark 과정 : 먼저 root로부터 그래프 순회를 통해 연결된 객체들을 찾아내어 각각 어떤 객체들을 참조하고 있는지 찾아서 마킹한다.
Sweep 과정 : 참조하고 있지 않은 객체, 즉 Unreachable 객체들을 힙에서 제거한다.
Compact 과정 : Sweep 후에 분산된 객체들을 힙의 시작 주소로 모아 메모리에 할당된 부분과 그렇지 않은 부분으로 압축한다. 다만, 가비지 컬렉터 종류에 따라 하지 않는 경우도 존재한다.

* Garbage Collection의 동작 과정
1. 첫번째 과정

객체가 처음 생성되고 힙 영역의 Eden 영역에 age-bit가 0으로 할당된다. 해당 age-bit는 Minor GC에서 살아남을 때마다 1씩 증가한다.



2. 두번째 과정

시간이 지나 힙 영역의 Eden 영역에 객체가 다 쌓이게 되면 Minor GC가 한번 일어나게 되고 참조 정도에 따라 Survival 0 영역으로 이동하거나 회수된다.



3. 세번째 과정

계속해서 Eden 영역에는 신규 객체들이 생성된다. 이렇게 또 Eden 영역에 객체가 다 쌓이게 되면 Young generation 영역에 있는 객체들을 비어있는 Survival인 Survival1 영역에 이동하고 살아남은 객체들은 age가 1씩 증가한다.



4. 네번째 과정

또 다시 Eden 영역에 신규 객체들로 가득 차게 되면 다시 한번 Minor GC가 발생하고 Young generation 영역에 있는 객체들을 비어있는 Survival인 Survival 0으로 이동시킨 뒤 age를 1씩 증가시킨다. 그리고 이 과정을 계속해서 반복한다.



5. 다섯번째 과정

이 과정을 반복하다 보면 age-bit가 특정 숫자 이상으로 되는 경우가 발생한다. 이 때 JVM에서 설정해놓은 age-bit에 도달하게 되면 오랫동안 쓰일 객체라고 판단하고 Old generation 영역으로 이동시킨다. 이 과정을 프로모션(Promotion)이라고 한다.



6. 마지막 과정

시간이 지나 Old generation 영역에 할당된 메모리가 허용치를 넘게 되면, Old generation 영역에 있는 모든 객체들을 검사하여 참조되지 않는 객체들을 한꺼번에 삭제하는 GC가 실행된다. 이렇게 OId genaration 영역의 메모리를 회수하는 GC를 Major GC라고 한다. Major GC는 시간이 오래 걸리는 작업이고 GC를 실행하는 스레드를 제외한 모든 스레드는 작업을 멈추게 된다. 이를 'Stop-the-World'라고 한다. 이 작업이 너무 잦으면 프로그램 성능에 문제가 발생할 수 있다. 따라서 어플리케이션의 사용성을 유지하면서 효율적이게 GC를 실행하는 최적화의 작업이 개발자의 숙제가 된다. 이러한 GC 최적화 작업을 GC 튜닝이라고 한다. GC 튜닝과 GC의 종류에 대해서는 다음 포스팅에서 다룰 예정이다.