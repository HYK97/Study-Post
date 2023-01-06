## HashMap이란?
HashMap은 Key, Value를 저장하는 Map의 구현체 중 하나이다. key를 넣으면 value값을 반환하게 되어 있다.

시작하기에 앞서,

## 📌먼저 보는 Tree로 변하는 이유

HashMap은 
> * This map usually acts as a binned (bucketed) hash table, but
      when bins get too large, they are transformed into bins of
   TreeNodes
   
   원래는 LinkedList 형식이지만 값이 너무나도 커지면 TreeNode로 바뀐다는 사실이다.
   
   > * The added complexity of tree bins is worthwhile in providing worst-case O(log n) operations when keys either have distinct hashes or are orderable.
   
   트리 빈의 추가된 복잡성은 키가 뚜렷한 해시를 가지거나 순서를 지정할 수 있는 최악의 경우 O(log n) 연산을 제공하는 데 가치가 있다. 즉, 시간복잡도 관련으로 로그 형태를 유지할 수 있어 값이 커져도 빠르게 사용할 수 있음을 나타낸다.
   
   값이 적을 때는 list, 커질 때는 tree로 바꿔서 사용하는 이유는 
   
   > * Because TreeNodes are about twice the size of regular nodes, we use them only when bins contain enough nodes to warrant use.
 (see TREEIFY_THRESHOLD). And when they become too small (due to removal or resizing) they are converted back to plain bins.
 트리 노드는 일반 노드의 약 두 배 크기이기 때문에, 우리는 빈에 충분한 노드가 포함되어 있을 때만 사용한다.
(TREYIF_임계값). 그리고 제거 또는 크기 조정으로 인해 너무 작아지면 일반 빈으로 다시 변환됩니다.
 
 
 메모리의 이슈인 것 같다.
 
 TREEIFY_THRESHOLD 즉, 트리로 변하게 되는 임계값이 정해져 있는데
 >The bin count threshold for using a tree rather than list for a bin. Bins are converted to trees when adding an element to a bin with at least this many nodes. The value must be greater than 2 and should be at least 8 to mesh with assumptions in tree removal about conversion back to plain bins upon shrinkage.
_ bin에 대한 목록이 아닌 트리를 사용하기 위한 bin 카운트 임계값입니다. 최소 이 개수의 노드가 있는 저장소에 요소를 추가하면 저장소가 트리로 변환됩니다. 값은 2보다 커야 하며 축소 시 일반 저장소로 다시 변환하는 것에 대한 트리 제거의 가정과 맞물리려면 8 이상이어야 합니다._
 
 ```java
 static final int TREEIFY_THRESHOLD = 8; // 트리 임계점 8
 ```
 
 이제 HashMap 의 put 메서드를 순차적으로 알아보자.
 

## HashMap 순서 (put)

![](https://velog.velcdn.com/images/yeb0/post/7cc06873-7081-4796-bc4e-dae1cc2bf143/image.png)


1

![](https://velog.velcdn.com/images/yeb0/post/d7d17125-e4f6-4903-bb15-698a76336a4e/image.png)
map 객체의 HashMap에 있는 put()함수를 호출한다. key 는 Integer 타입의 1, value는 String 타입의 "one"이 들어가는 중이다.

2
![](https://velog.velcdn.com/images/yeb0/post/9ff65f45-e847-4470-a5df-1f39dfe5e029/image.png)
key값이 null이라면 0을 return, 그게 아니라면 Object 클래스의 hashCode() 메서드를 호출한다. 메서드를 실행한 후 return 값인 int h의 값을 얻어낸 후 오른쪽으로 16번 부호없는 우측 시프트연산  (**unsigned right shift**)) 한 값과 XOR 하여 hash 값을 얻는다. hash 값을 얻는 이유는 이렇다. hashCode는 객체의 메모리 번지를 이용해서 해시 코드를 만들어 리턴하기 때문에 객체마다 다른 값을 가지게 된다. 따라서 객체마다의 주소를 정하기 위해 얻는다.


### 📌unsigned right shift ?
 unsigned 이란 음수를 표현하지 않는다는 것임.
 즉, 다시 말해 오른쪽으로 이동 후 왼편에 남아 있는 빈 비트 공간을 부호와 무관하게 0 으로 채운다는 말이다.

3
![](https://velog.velcdn.com/images/yeb0/post/273ae363-cf49-4dca-99b3-6acc4f47297b/image.png)
value값으로 1이 들어감

4
![](https://velog.velcdn.com/images/yeb0/post/38bf1812-a69d-4e10-8035-cc97c37e073f/image.png)
위에서 return 받은 value(1)값을 매개변수로 받아 다시 return한다.
5

![](https://velog.velcdn.com/images/yeb0/post/6a698599-2aa5-4791-a6a6-eebed70f2db8/image.png)
다시 return

6
![](https://velog.velcdn.com/images/yeb0/post/fda2f924-2a8c-45e6-984c-6b14c42e8923/image.png)
왜 다시 key값으로 1이 들어가 연산하는지  모르겠음.

7
![](https://velog.velcdn.com/images/yeb0/post/66da9cc6-9f48-4dcc-9bda-09eb4e578af4/image.png)
해당 key 값 1과 value "one", onlyIfAbsent, evict, 값 그리고 위에서 얻어낸 hash값을 넣는다.

8
![](https://velog.velcdn.com/images/yeb0/post/8e798edb-b58a-4abd-9ada-242cebe240df/image.png)
Node가 비어 있어서 null이 참이기에 바로 밑 로직을 실행한다.

9
![](https://velog.velcdn.com/images/yeb0/post/8a6ac0b5-6ab1-496d-b1f6-5033c9779e3c/image.png)
값을 처음 넣는 거라 table이 null이기에 크기를 초기화한다. resize()로 넘어간다.

10
![](https://velog.velcdn.com/images/yeb0/post/5b7c55dc-69e2-41af-bbab-12712f31d9c0/image.png)
멤버변수 table을 oldTab Node배열에 넣는다.

11
![](https://velog.velcdn.com/images/yeb0/post/f86e3e82-0a81-496e-bfe6-f2585d7479c9/image.png)
아직은 없기에 oldTab은 null이며, 0을 oldCap에 반환

12
![](https://velog.velcdn.com/images/yeb0/post/adc24158-dc1a-4890-96a6-1d0e6fccc210/image.png)

threshold를 oldThr에 대입한다.
13
![](https://velog.velcdn.com/images/yeb0/post/13aeb9a8-9785-43d3-a433-8ab2597fe020/image.png)
초기화부분

14
![](https://velog.velcdn.com/images/yeb0/post/512b0c4a-f1f6-497d-8350-1593120087d2/image.png)
앞에서 oldCap이 0으로 리턴받았기에 해당 if문을 통과한다. 바로 밑인 else if문으로 들어간다.


15

![](https://velog.velcdn.com/images/yeb0/post/67433eb7-aeaa-4f94-be2a-4f0a1b055b2d/image.png)
위에서도 oldThr 변수는 0으로 지정받았기에 false

16

![](https://velog.velcdn.com/images/yeb0/post/ce4dbd8f-6974-4aeb-9739-e4a9a404b95e/image.png)
### DEFAULT_INITIAL_CAPACITY
바로 밑인 else문 실행한다. DEFAULT_INITIAL_CAPACITY가 무엇일까? 위에서 선언 되어 있었다.
> 기본 초기 용량 - 반드시 2의 거듭제곱이어야 합니다.

```java
	static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
```
크기를 지정하는 것일까? 이 부분은 이해가 되질 않는다. 주석처리에 aka 16이라 정해져 있었고,

17

![](https://velog.velcdn.com/images/yeb0/post/c99fd321-a973-4ec4-a548-d7633ed1de3b/image.png)
### DEFAULT_LOAD_FACTOR
코드가 넘어가면서 newCap에 16값이 들어가 있는 것을 확인했다.
마찬가지로, DEFAULT_LOAD_FACTOR란 ?

> 생성자에 지정되지 않은 경우 사용되는 부하 계수입니다.

```java
static final float DEFAULT_LOAD_FACTOR = 0.75f;
```

부하 계수로 임계값 0.75를 잡는다는데, 왜 0.75 값을 잡았는지는 
https://stackoverflow.com/questions/61341274/why-is-loadfactor-in-hashmap-is-set-to-0-75-by-default 를 참고하자. 그곳의 답변을 인용하자면,
> 아주 좋은 HashCode 구현을 사용하더라도 충돌이 있을 수 있기 때문이다. 맵의 요소 수가 많을 수록 충돌 가능성이 높아진다. 맵이 75% 채워지면 충돌 빈도가 증가. put을 유지하고 O(1) 주변에서 복잡성을 얻기 위해 약 0.75의 LOAD_FACTOR를 갖는 것이 좋다.


결국 효율성의 문제인 거 같다. 어느정도를 넘어가면 Map의 효율이 나오지 않아 해당 기준점을 설정한 것이다. 때문에 두 값을 곱하여 



18

![](https://velog.velcdn.com/images/yeb0/post/74f9e161-158e-4d99-8a65-5fb459ba6139/image.png)

newThr 변수에 12값이 들어가 false가 나오는 상황이다.




19
![](https://velog.velcdn.com/images/yeb0/post/9ea0d631-2dbf-4d4c-b21c-b74959e08e3b/image.png)
#### threshold
threshold에 12값을 대입해 준다. 
threshold는? 어떤 특정값 이상 이하라는 뜻이다. 임계점인지는 잘 모르겠다.


20
![](https://velog.velcdn.com/images/yeb0/post/56180756-8797-4374-8086-04d27d982183/image.png)
새 Node 배열 newTab newCap(16)값을 넣어 설정해 준다.


21
![](https://velog.velcdn.com/images/yeb0/post/080c9dff-aee6-4f04-8cf3-1556fa6e5b15/image.png)
newTab배열은 곧 멤버변수 table 에 대입하게 되고 10번에서 멤버변수 table을 oldTab Node배열에 넣었지만 newTab배열은 다시 table 변수에 들어가게 되어 null값이 아니게 된 상황이다.

22

![](https://velog.velcdn.com/images/yeb0/post/b6d93a94-a7ec-41b5-9a0d-d9d6f1fce425/image.png)
false인 부분.


23

![](https://velog.velcdn.com/images/yeb0/post/5a3edabc-c918-41e9-b716-e45b9f905b1f/image.png)
해당 if문이 false이므로 newTab (table) 을 return한다.

24

![](https://velog.velcdn.com/images/yeb0/post/e96a52b6-71a7-47b8-93a1-5af36fb5e6c6/image.png)

25

![](https://velog.velcdn.com/images/yeb0/post/55f579e2-bccd-4f69-b133-813e292132f3/image.png)
key 중복이 없을 때 새로운 노드 추가.
26

![](https://velog.velcdn.com/images/yeb0/post/bc75abe2-d024-4d9c-8378-af80ffca34c7/image.png)
위에서 newTab에 16값을 줬기에 n값은 16으로 들어와 있는 상황.
해당 Node에 hash, key, value, tab(배열인 것 같음), i가 들어감
27

![](https://velog.velcdn.com/images/yeb0/post/61ea6871-8254-44a1-8ecb-8c9aa8fce51f/image.png)
hash, key( 1 ), value ( "one" ), next(null인 이유는 지금 첫번째로 값을 넣었기에 다음 값이 없어서이다.)


28
![](https://velog.velcdn.com/images/yeb0/post/d4c1af8e-01d4-4653-b2d3-c161da5db580/image.png)
생성자 호출

29
![](https://velog.velcdn.com/images/yeb0/post/c6d4ea4a-4877-45bf-975b-287bc82aa55e/image.png)
Object는 모든 클래스의 가장 최상위이기에 기본생성자 호출

30

![](https://velog.velcdn.com/images/yeb0/post/3f82f7f8-2df7-40a3-8a13-380cd22c6c76/image.png)
위에서 받아 왔던 값들 삽입

31
![](https://velog.velcdn.com/images/yeb0/post/9cd94029-89a4-49ce-b9c2-9b8cbb6e6a36/image.png)
삽입되고 있는 상황.

32

![](https://velog.velcdn.com/images/yeb0/post/ceb1786c-c093-41c2-a670-285115683786/image.png)
해당 사진에서는 잘려 있지만 위에 부가 설명으로
```java
	/*
     * The following package-protected methods are designed to be
     * overridden by LinkedHashMap, but not by any other subclass.
     * Nearly all other internal methods are also package-protected
     * but are declared final, so can be used by LinkedHashMap,view
     * classes, and HashSet.
     */
```
이라 적혀 있다. 패키지로 보호되는 방법을 말하고 있다. LinkedHashMap에 의해 재정의 되지만 다른 하위 클래스에는 재정의 되지 않으며 최종으로 선언되어 LinkedHashMap에서 사용할 수 있다고 한다.

마지막으로, 일반(트리가 아닌) 노드 생성이라 적혀져 있다. 위에서도 봤지만 HashMap은 LinkedList로 이어져 있다는 것을 알 수 있음.



33

![](https://velog.velcdn.com/images/yeb0/post/28383052-18e4-4744-a0f6-37334f33ff3e/image.png)

다시 값이 들어가는 상황

34

![](https://velog.velcdn.com/images/yeb0/post/8d5e802c-c41f-4e0f-b2a3-06f058332886/image.png)
### modCount

modCount가 추가가 되는데 무슨 말일까?

>이 HashMap이 구조적으로 수정된 횟수 구조적 수정은 HashMap의 매핑 수를 변경하거나 내부 구조를 수정하는 것입니다(예: rehash). 이 필드는 HashMap의 Collection-views에서 반복자를 빠른 속도로 만드는 데 사용됩니다. (ConcurrentModificationException 참조).

```java
transient int modCount;
```
간단하게 설명하자면 modCount는 컬렉션의 구조(크기)가 변경되는 횟수이다. 
https://docs.oracle.com/javase/7/docs/api/java/util/AbstractList.html#modCount 이곳에 modCount가 설명되어 있다.


35

![](https://velog.velcdn.com/images/yeb0/post/39405bf2-a1ca-497c-a46f-4782ace7c772/image.png)
현재 size는 0인 상황, threshold는 12로 대입되어 있는 상황이기에 false. 아래에 있는 resize();를 통과한다.

36

![](https://velog.velcdn.com/images/yeb0/post/ff780ffa-c6d7-42e2-8a3f-945faf79a627/image.png)
### afterNodeInsertion
afterNodeInsertion 이란?
>// Callbacks to allow LinkedHashMap post-actions
// LinkedHashMap 사후 조치를 허용하는 콜백

```java
void afterNodeInsertion(boolean evict) { }
```
이라 나와 있는데, 이벤트 콜백성 코드라 한다. 왜 true값을 나타내는지 모르겠음.


37

![](https://velog.velcdn.com/images/yeb0/post/82ed1c0b-bf1d-4d2e-befe-f1ef54c0c0e6/image.png)


38
![](https://velog.velcdn.com/images/yeb0/post/ab1c81d7-8140-4d95-84ae-ebf78cb08bf0/image.png)
true값을 받아 null을 return한다.


39
![](https://velog.velcdn.com/images/yeb0/post/f67900ff-53da-4834-8a73-39dcf5ea9a35/image.png)
key값에 대한 value값이 저장된 상황.

40

![](https://velog.velcdn.com/images/yeb0/post/77cc995b-7ba0-4d44-b239-4ddcbf7dbdd6/image.png)
put 첫단계 size는 1이며 키와 값이 들어가 있는 상태.

이제 두 번째 값을 넣는 차례.

41

![](https://velog.velcdn.com/images/yeb0/post/c641c9a7-96ee-44ed-9f4c-274a08b5ac36/image.png)
같은 key값에 다른 value값을 넣어봤다.

42
![](https://velog.velcdn.com/images/yeb0/post/2636fc61-5759-484e-9f42-0c466324e99d/image.png)

43

![](https://velog.velcdn.com/images/yeb0/post/8d41dcca-585f-47c5-9a02-190fbfb0b3b0/image.png)

44

![](https://velog.velcdn.com/images/yeb0/post/580d13ee-b9c4-4324-88a4-e2d272d39c8e/image.png)

45

![](https://velog.velcdn.com/images/yeb0/post/d5ea55c1-e9c6-477d-a490-df25c1b2dfe7/image.png)
key값 1, value값 "two"가 들어가려는 상황.

46

![](https://velog.velcdn.com/images/yeb0/post/f3673adc-008f-4211-a699-7109a547166c/image.png)


47
![](https://velog.velcdn.com/images/yeb0/post/6c91f530-7ab0-451b-aeec-bd217f1894f1/image.png)

48

![](https://velog.velcdn.com/images/yeb0/post/f747750c-ee64-45cc-970b-bdb34bbecd9f/image.png)


49
![](https://velog.velcdn.com/images/yeb0/post/70bbf9ce-08e7-49b5-a03a-c9e3823787f1/image.png)

50

![](https://velog.velcdn.com/images/yeb0/post/bf9a425e-efce-4728-8694-14c09f839879/image.png)

51

![](https://velog.velcdn.com/images/yeb0/post/f1429eb7-c70b-4970-a687-bac9f6538af0/image.png)

52

![](https://velog.velcdn.com/images/yeb0/post/da1e4580-aa35-493b-b616-3d2d03a53eac/image.png)
기존에 값 key 1 , value "one"이 들어가 있기에 둘 다 false인 상황. 따라서 밑의 if문으로 넘어간 상황이다.

53

![](https://velog.velcdn.com/images/yeb0/post/c97808d0-d8c5-4abc-900b-af593e570124/image.png)
만약 존재하는 해시 값이라면 
1. 키 값이 같아서 해시 값이 같은가? or
2. 키 값이 다른데 해시 값이 같아서 해시 충돌이 난건지를 아래(로직들)에서 구분한다.

54
![](https://velog.velcdn.com/images/yeb0/post/3606336a-ce46-49fa-905b-5e881287b5e1/image.png)
먼저 서로의 hash가 같은지부터 확인한다.

55

![](https://velog.velcdn.com/images/yeb0/post/a09a90d1-19ea-4607-aee8-58cb1d983d49/image.png)
hash가 같으며 키값도 같은 경우 1 -> "one" 

56

![](https://velog.velcdn.com/images/yeb0/post/61cdc33d-81fe-472b-a16e-7b04bd0bd0ff/image.png)

그래서 e 값에 value값이 들어가 null이 아니기에 참인 경우이다.
57

![](https://velog.velcdn.com/images/yeb0/post/fe44b617-46d9-4dc5-b5dd-c419f2469a3c/image.png)
e의 value값은 (e : 1 -> "one")인 상태.

58
![](https://velog.velcdn.com/images/yeb0/post/e4a7b93d-466a-4d33-b2c4-d62245523c78/image.png)
### onlyIfAbsent
onlyIfAbsent란? 없을 경우에만... 이라는 뜻이다. true가 올 경우, 존재하는 값을 바꾸지 않는다는 말이다. 위에서 onlyIfAbsent : false이기에 값을 바꾼다는 것이다.


59
![](https://velog.velcdn.com/images/yeb0/post/77a4d344-bfa2-4709-8084-4da5d0288eb5/image.png)
그래서 value 값 ("two")은 e.value (e: 1 -> "one")에 대입하게 되고

60
![](https://velog.velcdn.com/images/yeb0/post/782caab7-725a-40ea-b543-31d0bee75905/image.png)
key값 1에 value값 "two"가 들어가 있는 상황이다.

61

![](https://velog.velcdn.com/images/yeb0/post/ea15a3e3-074c-4a67-abd2-ef8318839605/image.png)
### afterNodeAccess
afterNodeAccess은 key 중복이 존재할 때 Node간의 link를 교체하는 메서드이다. 해당 예제에서는 모든 key 값을 1로 잡았기에 해당 메서드가 실행된다.

62

![](https://velog.velcdn.com/images/yeb0/post/3bb5fb98-ffd2-4f3f-ab13-f28584968f38/image.png)
oldValue는 여전히 "one"인 상황.

63

![](https://velog.velcdn.com/images/yeb0/post/3b063ce6-c0b7-4668-9e26-dbfc01349f4d/image.png)
key 값은 1, 해당 key 값에 대한 value값은 "two"로 바뀐 상황.

64

![](https://velog.velcdn.com/images/yeb0/post/80115c83-9088-4a3b-a99b-1529d6a75d6b/image.png)
동일한 key 값에 link 연결로 1 -> "one" "two"가 이어진 상황이며 해당 size는 1인 상황이다.

그래서 일단 "two" 까지만 put 했다고 생각하고, 해당 map을 출력하자면 이렇다.

```java
map.put(1, "one");
map.put(1, "two");
for (Integer integer : map.keySet()) {
            System.out.println(map.get(integer));
}
```
출력 결과로는 **two** 하나만이 출력된다. 왜일까? 
1의 key값에 마지막 value값으로 two가 들어왔으며, hashCode가 같으며 key값이 같기에 하나만이 출력된다. 그럼 hashCode가 다르다면 어떻게 출력 될까? 테스트를 위해 Test class를 만들자.
```java
    static class Test{
        int value;
        String string;
		
        //생성자 선언
        public Test(int value, String string) {
            this.value = value;
            this.string = string;
        }
		
        //해당 key와 value를 출력하기 위한 오버라이딩
        @Override
        public String toString() {
            return "Test{" +
                    "value=" + value +
                    ", string='" + string + '\'' +
                    '}';
        }
		
		//Object 클래스의 equals 오버라이딩
        @Override
        public boolean equals(Object o) {
        //매개변수로 들어온 o 가 this (one) 같다면
        //같은 hashCode를 가짐.
            if (this == o) return true;
        //만약 다른 hashCode라면 해당 if문 실행
        //one -> two 타입이 다른 경우 false
            if (o == null || getClass() != o.getClass()) return false;
        //hashCode도 같고 타입도 같다면 Test 형변환 (모두 같기에 믿을 수 있음)
            Test two = (Test) o;
        //one -> two 실제값이 같은 경우 true
        //one -> two 실제값이 다른 경우 false
            return value == two.value && Objects.equals(string, two.string);
        }
		//Object 클래스의 equals 오버라이딩
        //같이 같은 경우, 같은 Hash 값을 return한다.
        @Override
        public int hashCode() {
            return Objects.hash(value, string);
        }
    }
```

![](https://velog.velcdn.com/images/yeb0/post/cf571ee3-856a-41e2-9a0f-e80bc7e95610/image.png)


value값과 String의 값들을 모두 같게 선언해 줬다. 보이지 않지만 Object에 상속 받았고 Test 클래스에서 메서드 오버라이딩을 했기에 Test 클래스의 hashCode 메서드를 사용할 수 있다. 



![](https://velog.velcdn.com/images/yeb0/post/c5822a88-8cfb-4430-8edc-8243b13f890f/image.png)

해당 변수들의 hashCode들을 출력해 보면
![](https://velog.velcdn.com/images/yeb0/post/55d43770-1b2d-4510-bdad-2b12719e8203/image.png)

값들이 나온다. 위에서 말했듯이 hashCode가 같고 key 값이 같다면 해당 key값을 호출했을 때 마지막 한 개의 값만이 호출되었다.

위에서, HashMap을 선언하고 두 개의 타입으로 key : Test,  value : String 선언하였다. 
![](https://velog.velcdn.com/images/yeb0/post/0e2842c2-ac3f-462b-ad1a-0ecf5c589801/image.png)

같은 key, hashCode라면 map에 put 했을 때 내가 원하는 값은 하나만이 나와야 한다. 출력하자면

```java
for (Test key: map.keySet()) {
    System.out.println("key : "+ key +" value : " + map.get(key));
}
```
결과 값은 이렇다.
```java
key : Test{value=1, string='one'} value : test3
```
one, two, three로 보여서 key 값은 달라 보이지만 해당 내부에는 Object 클래스의 equals를 통해 동일한 hashCode임을 알았고, 실제값도 같음을 알 수 있기에 같다고 말할 수 있다. 위에서 value값과 String 값을 1과 "one"으로 잡았지만, 둘 중 하나라도 달라진다면 hashCode의 값은 서로 다르게 나타나며 다른 값으로 받아들여 key값이 달라짐을 알 수 있다.

그 이유는 밑에서 Obeject 클래스의 equals 메서드와 hashCode를 오버라이딩하고 재정의하면서 서로의 값이 같아야만 같은 값으로 인정하기 때문이다.

hashCode와 key값이 동일하다면 같은 값으로 취급한다 했는데, 그럼 equals 메서드를 주석처리하고 실행해보면 같은 출력이 나올까?

![](https://velog.velcdn.com/images/yeb0/post/5088ce25-071e-43b5-874c-b2a8f696fe01/image.png)


hashCode는 같지만 동일한 값이 나오지 않았다. 이유는 Object 클래스의 equals 메서드에서 값을 비교하며 마지막 return 부분에서 실제 값이 같은 경우 true를 반환하기에 걸러지는 부분이 없어져서 값이 다르다고 판단하게 되기 때문이다.
