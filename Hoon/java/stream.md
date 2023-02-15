금일은 02.15에 있을 java-study 에서 다룰 주제인 Stream에 대하여 공부했다.
내가 맡은 주제는 stream의 주의사항, 속도관련이슈인데.. 어제는 주의사항으로 골머리를 앓았고, 오늘은 
속도 관련으로 머리 좀 싸맸다.


먼저, 내가 알아야 할 것은 stream의 사용법이었으며 눈에 익히는 데에는 큰 어려움은 없었다.

해당 코드는 **자바의 신 2부 책**에서 공부한 내용을 토대로 작성하였음을 밝힙니다.

```java
public class StudentForEachSample {
    public static void main(String[] args) {
        StudentForEachSample sample = new StudentForEachSample();
        List<StudentDTO> studentList = new ArrayList<>();
        studentList.add(new StudentDTO("요다", 43, 99, 10));
        studentList.add(new StudentDTO("만두", 30, 71, 85));
        studentList.add(new StudentDTO("건빵", 32, 81, 75));
        sample.printStudentNames(studentList);
        studentList.stream().map(student -> student.getName()).collect(Collectors.toList());
    }
    public void printStudentNames(List<StudentDTO> students) {
        students.stream().forEach(student -> System.out.println(student.getName()));
//        students.stream().map(student -> student.getName()).forEach(name -> System.out.println(name));
        students.stream().forEach(student -> System.out.println(
                student.getName()
                + " " + student.getAge()
                + " " +student.getScoreMath()
                + " " +student.getScoreEnglish())
        );
    }
}
```

간단히 forEach와 Stream의 차이, stream 내부의 map() 메서드와 filter() 메서드를 알아봤다.

```java
public class StreamFilterSample {
    public static void main(String[] args) {
        StreamFilterSample sample = new StreamFilterSample();
        List<StudentDTO> studentList = new ArrayList<>();
        studentList.add(new StudentDTO("요다", 1, 1, 1));
        studentList.add(new StudentDTO("만두", 2, 2, 2));
        studentList.add(new StudentDTO("건빵", 3, 3, 3));
        sample.filterWithScoreForLoop(studentList, 2);
//        studentList.stream().filter(student -> student.getScoreEnglish() > scoreCutLine)
//                  .forEach(student -> System.out.println(student.getName());
    }
    public void filterWithScoreForLoop(List<StudentDTO> studentList, int scoreCutLine) {
        for (StudentDTO student : studentList) {
            if (student.getScoreEnglish() > scoreCutLine) {
                System.out.println(student.getName());
            }
        }
    }
}
```

그 다음, 주제 중 하나인 Stream을 사용할 때의 주의사항인데... 이것은 조금 더 다시 공부하여 작성할 예정이다.
속도 관련인데, 아주 흥미로웠다. 먼저 코드를 보자면.. 아. 보기 전에 앞서서 자신의 컴퓨터의
코어(논리 프로세서) 개수를 찾는 로직을 알아냈다. 

```java
public class HowMuchOfAvailableProcessors {
    public static void main(String[] args) {
        int numOfCores = Runtime.getRuntime().availableProcessors(); // 본인 컴퓨터 코어(논리 프로세서)개수 찾는 로직.
        System.out.println(numOfCores);
    }
}
```
본인의 컴퓨터 코어는 6개이며, 논리 프로세서는 12개이므로 12가 출력이 된다. 아주 신기함.
물론 이것을 그냥 적는 것은 아니고.. 이유가 따로 있다. 그 이유는 밑의 코드를 보면 알 수 있다.
(이것만 보고 속도 관련에 관한 내용 작성하겠음.)

```java
public class StreamExamplesParallel {
    public static void main(String[] args) {
        final int[] sum = {0};
        IntStream.range(0, 100)
                .forEach(i -> sum[0] += i); // single Thread
        System.out.println(" stream sum = " + sum[0]); // 4950
        final int[] sum2 = {0};
        IntStream.range(0, 100)
                .parallel() // Multi  process 를 활용하여 여러 개의 스레드가 동시에 sum2의 상태를 바꾸는 것
                .forEach(i -> sum2[0] += i);
        System.out.println(" parallel sum = " + sum2[0]); // 값이 계속 변함 ( 4950 근처긴 함 )
        System.out.println("Stream sum = " + IntStream.range(0, 100)
                .sum());
        System.out.println("parallel stream sum = " + IntStream.range(0, 100)
                .parallel()
                .sum());
        System.out.println("------Stream------");
//        final long start = System.currentTimeMillis();
//        Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13) // 1초 당 하나씩 뽑아냄.
//                .stream()
//                .map(i -> {
//                    try {
//                        TimeUnit.SECONDS.sleep(1);
//                    } catch (InterruptedException e) {
//                        e.printStackTrace();
//                    }
//                    ;
//                    return i;
//                })
//                .forEach(i -> System.out.println(i));
//        System.out.println(System.currentTimeMillis() - start); // Millis 단위라 천 단위로 나옴. 예상 출력안 -> 13XXX
        System.out.println("------parallel Stream ( 12 elements )------");
        final long start2 = System.currentTimeMillis();
        Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12) // 내 컴퓨터 코어 수 6개라서 논리 프로세서 12개. 1초만에 끝나야 함.
                .parallelStream()
                .map(i -> {
                    try {
                        TimeUnit.SECONDS.sleep(1);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    ;
                    return i;
                })
                .forEach(i -> System.out.println(i));
        System.out.println(System.currentTimeMillis() - start2); // Millis 단위라 천 단위로 나옴. 예상 출력안 -> 1XXX
        System.out.println("------parallel Stream ( 13 elements )------");
        final long start3 = System.currentTimeMillis();
        Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13) // 내 컴퓨터 코어 수 6개라서 1초만에 끝나야 함. 6코어인데... 7개면 2초여야 되는 것을. 13개 찍어야 2초로 시작함. 왜? 2배가 늘지..? -> 코어 수가 6개라 논리 프로세서는 12개. 한번에 일할 수 있는 최대 수는 12라서 그런듯?
                .parallelStream()
                .map(i -> {
                    try {
                        TimeUnit.SECONDS.sleep(1);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    ;
                    return i;
                })
                .forEach(i -> System.out.println(i));
        System.out.println(System.currentTimeMillis() - start3); // Millis 단위라 천 단위로 나옴. 예상 출력안 -> 2XXX
        System.out.println("------parallel Stream ( 12 elements ) with parallelism : 11 ------");
//        System.setProperty("java.util.concurrent.ForkJoinPool.common.parallelism", "11"); // 0이라면 single core, 1이라면 2 core, 2라면 3 core... 본인 6코어인데 논프 12이니 11 박을 예정
//        final long start4 = System.currentTimeMillis();
//        Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12)
//                .parallelStream()
//                .map(i -> {
//                    try {
//                        TimeUnit.SECONDS.sleep(1);
//                    } catch (InterruptedException e) {
//                        e.printStackTrace();
//                    }
//                    ;
//                    return i;
//                })
//                .forEach(i -> System.out.println(i));
//        System.out.println(System.currentTimeMillis() - start4); // Millis 단위라 천 단위로 나옴. 예상 출력안 -> 1XXX
        System.out.println("------parallel Stream ( 12 elements ) with parallelism : 5 ------");
        System.setProperty("java.util.concurrent.ForkJoinPool.common.parallelism", "5"); // 코어 수를 반으로 제한
        final long start5 = System.currentTimeMillis();
        Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12)
                .parallelStream()
                .map(i -> {
                    try {
                        TimeUnit.SECONDS.sleep(1);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    ;
                    return i;
                })
                .forEach(i -> System.out.println(i));
        System.out.println(System.currentTimeMillis() - start5); // Millis 단위라 천 단위로 나옴. 예상 출력안 -> 2XXX
    }
}
```

굉장히 길지만, 설명해 보자면.. (주석처리한 코드도 다 기재할 것임.)

* 첫 번째 로직은 stream을 이용하여 forEach를 활용해 0부터 100까지 더하는 값
* 두 번째 로직은 stream을 이용하여 parallel()을 활용, forEach를 활용하여 0부터 100까지 더하는 값
* 세 번째, 네 번째는 각각 stream을 이용하여 sum() 함수를 활용,네 번째는 parallel()만 써서 더하는 값이다.

결과는 두 번째를 제외한 나머지는 4950, 두 번째는 4950보다 낮은 근처의 값으로 랜덤으로 출력한다.

이유는 ? 주석에도 달아놨지만 Multi  process 를 활용하여 여러 개의 스레드가 동시에 sum2의 상태를 바꾸는 것이기 때문이다.
single thread와는 다르게 여러 개의 thread 들이 서로 더한 값으로 더해져서 근사값으로 나오는 것이다.

여기까진 됐고, 그럼 이제 각각 stream 과 parallel() 함수, 그리고 본인의 컴퓨터의 코어의 수만큼 
일처리를 어떻게 잘하는지 확인하는 로직들을 볼 것이다.

먼저 내 컴퓨터의 정보는 코어 6, 논리 프로세서 12이다. 때문에 12개의 스레드들이 다같이 한 번 일을 할 수 있으며


첫 번째 로직에서는 1부터 13까지 list를 하나씩 출력하며, 1초씩 TimeUnit.SECONDS.sleep(1)을 줬다. 그럼 당연히 예상할 수 있는
결과값은 13XXX로 나와야할 것이다. (13초, currntTimeMillis()이기에.) -> 결과로 잘 나온다.
```java
final long start=System.currentTimeMillis();
        Arrays.asList(1,2,3,4,5,6,7,8,9,10,11,12,13) // 1초 당 하나씩 뽑아냄.
                .stream()
                .map(i->{
                    try{
                        TimeUnit.SECONDS.sleep(1);
                    }catch(InterruptedException e){
                        e.printStackTrace();
                    };
        return i;
        })
        .forEach(i->System.out.println(i));
    System.out.println(System.currentTimeMillis()-start); // Millis 단위라 천 단위로 나옴. 예상 출력안 -> 13XXX
```


두 번째 로직에서는 parallelStream() 함수를 사용했다. 병렬처리를 한다는 것인데, 12개의 논리 프로세서가 있고 12개의 요소가 있다면
각각의 프로세서들은 하나씩 맡아 처리를 한다고 치면 1초(1XXX)만에 끝나야 한다. -> 결과가 잘 나옴.

```java
final long start2 = System.currentTimeMillis();
        Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12) // 내 컴퓨터 코어 수 6개라서 논리 프로세서 12개. 1초만에 끝나야 함.
                .parallelStream()
                .map(i -> {
                    try {
                        TimeUnit.SECONDS.sleep(1);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    ;
                    return i;
                })
                .forEach(i -> System.out.println(i));
        System.out.println(System.currentTimeMillis() - start2); // Millis 단위라 천 단위로 나옴. 예상 출력안 -> 1XXX
```

세 번째 로직에서는 요소를 하나 더 추가해 13개가 되었으며, 12개가 작업이 다 끝나면 딱 한 번의 작업을 더 해야하기에
2초(2XXX)가 나와야 한다. -> 잘 나옴

```java
final long start3 = System.currentTimeMillis();
        Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13)  //내 컴퓨터 코어 수 6개라서 1초만에 끝나야 함. 6코어인데... 7개면 2초여야 되는 것을. 13개 찍어야 2초로 시작함. 왜? 2배가 늘지..? -> 코어 수가 6개라 논리 프로세서는 12개. 한번에 일할 수 있는 최대 수는 12라서 그런듯?
                .parallelStream()
                .map(i -> {
                    try {
                        TimeUnit.SECONDS.sleep(1);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    ;
                    return i;
                })
                .forEach(i -> System.out.println(i));
        System.out.println(System.currentTimeMillis() - start3); // Millis 단위라 천 단위로 나옴. 예상 출력안 -> 2XXX
```

처음에는 이해가 잘 안됐다. 애초에 코어수가 6으로 되어 있어서 요소도 6개로 만들고 시작했었다. (위의 예제들 전부)
그런데 start3에서 이슈가 생긴 것이었다...2초가 나올 것을 예상하여 요소를 7개로 만들고 찍었더니 그래도 1초, 8로도..9로도.. 그러다 13개를 찍으니
2초로 늘어난 것. 알고 봤더니 논리 프로세서를 뜻하는 것이었고, CS 지식이 부족하여 나 혼자만의 이슈였던 것이었다......
CS 공부 열심히 할 것 ㅜㅜ

아무튼, 잘 나온다.

다음은 네 번째. 코어 수를 제한하는 것으로, 새로운 로직이 추가 되는데 사실 아예 처음 보는 로직이라 이해가 되질 않았지만 코어수를 제한한다. 라고 생각 하고 보았다.
```java
System.setProperty("java.util.concurrent.ForkJoinPool.common.parallelism", "11"); // 0이라면 single core, 1이라면 2 core, 2라면 3 core... 본인 6코어인데 논프 12이니 11 박을 예정
        final long start4 = System.currentTimeMillis();
        Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12)
                .parallelStream()
                .map(i -> {
                    try {
                        TimeUnit.SECONDS.sleep(1);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    ;
                    return i;
                })
                .forEach(i -> System.out.println(i));
        System.out.println(System.currentTimeMillis() - start4); // Millis 단위라 천 단위로 나옴. 예상 출력안 -> 1XXX
```

key 값으로 "java.util.concurrent.ForkJoinPool.common.parallelism"을 주며, value 값으로 예를 들어 0이라면 core 1개, 1이라면 2개..인 식이다.

나는 12개이니 11을 value로 넣어준다면 1초가 나와야 한다. -> 잘 나옴.

마지막으로 다섯 번째. 그렇다면 core 수가 반으로 줄어든다면 2초로 나와야한다.

```java
System.setProperty("java.util.concurrent.ForkJoinPool.common.parallelism", "5"); // 코어 수를 반으로 제한
        final long start5 = System.currentTimeMillis();
        Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12)
                .parallelStream()
                .map(i -> {
                    try {
                        TimeUnit.SECONDS.sleep(1);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    ;
                    return i;
                })
                .forEach(i -> System.out.println(i));
        System.out.println(System.currentTimeMillis() - start5); // Millis 단위라 천 단위로 나옴. 예상 출력안 -> 2XXX
```
2초로 잘 나온다 ! 다시 한 번 더 5가 아닌 3 즉, core 4로 치자면 3초가 나와야 한다. 그것도 실행해본 결과 아주 잘 나온다.

여기서 더 추가로 이슈를 하나 발견했었는데, 해당 로직들 주석을 모두 해제하고 다 돌릴 경우 마지막인 다섯 번째 로직이 
이상하게 예상치 못한 결과가 연속이었다. 예를 들어 2초가 나와야하는데 1초만 계속 나온다던지..?

알아 봤더니 원인은 여러 가지이다. 
>1. JVM레벨 설정이므로 예기치 못한 곳에서 원치 않은 core 사용or비사용 이슈
>2. System.setProperty로 parallelism을 설정하기 전 parallelStream()을 사용할 경우 이미 생성된 ***ForkJoinPool**을 내부적으로 그대로 사용하기에 설정이 바뀌지 않는다는 것. (내가 겪은 이슈)
그래서 나는 모든 코드를 주석으로 처리하고 따로 확인하여 결과를 체크했다.

### 📌 여기서 ForkJoinPool 이란 ?

작업을 분할가능할 만큼 쪼개고 쪼개진 작업을 별도의 작업 thread을 통해 작업 후 결과를 합치는 과정을 거쳐 결과를 만든다.
병렬 stream의 ForkJoin Framework의 작업 thread의 수는 service가 돌아가는 컴퓨터 CPU core 수의 종속됨.
그래서 위의 setProperty로 지정해두거나, parallelStream을 사용했을 때 내부적으로 돌아가서 core 수로 써진다는 것.


추가로 멀티프로세스에 관련해 주석처리로 공부했던 것을 작성해놓겠다.

```java
/**
 * 1. 멀티 프로세스 ?
 * -> 여러 개의 프로세스가 서로 협력적으로 일을 처리하는 것. ( 밑의 예제 ) == 작업을 병렬처리 하는 것.
 * 각 프로세스 간 메모리의 구분이 필요하거나 독립된 주소 공간을 가져야 할 때 사용한다.
 *
 * 📌장점 ?
 * 독립된 구조이기에 안정성 높음, 여러 프로세스가 작업을 같이 하고 있어서 하나의 프로세스가 죽어도 문제가 확산되지 X (느려질 수는 있음. 죽은 프로세스만큼 일을 더 해야함)
 * 여러 개의 프로세스가 처리되어야 할 때, 동일한 데이터를 사용하며 이 데이터를 하나의 disk에 두고 모든 프로세서(cpu)가 이를 공유하면 비용적으로 저렴함.
 *
 * 📌단점?
 * 멀티 스레드보다 많은 메모리 공간과 cpu 시간을 차지함.
 * 독립된 메모리 영역이기에 작업량이 많을 수록 (*Context Switching이 자주 일어나서 주소 공간의 공유가 잦을 경우) 오버헤드가 발생하여 성능 저하 발생 우려
 * Context Switching 과정에서 캐시 메모리 초기화 등 무거운 작업이 진행되고 시간이 소모되는 등 오버헤드 발생.
 *
 * 📌Context Switching ?
 * cpu는 한 번에 하나의 프로세스만 실행 가능.
 * cpu에서 여러 프로세스를 돌아가면서 작업을 처리하는데 이 과정을 말함.
 * 동작 중인 프로세스가 대기하면서 해당 프로세스 상태(context)를 보관하고,
 * 대기하고 있던 다음 순서의 프로세스가 동작하면서 이전에 보관했던 프로세스의 상태를 복구하는 작업.
 * ---------------------------------------------------------------
 *
 */
```

parallelStream 속도 이슈도 한번 
적어보자. 우선은 전체 코드부터 적어놓고 하나하나 다시 짚어보자.

```java
 public static long iterativeSum(long n) {
        /*
        사실 실용적인 코드는 아님.
         */
        long result = 0;
        for (long i = 0; i <= n; i++) {
            result += i;
//            slowDown(); // 추가
        }
        return result;
    }
    public static long sequentialSum(long n) {
        return Stream
                .iterate(1L, i -> i + 1)
                .limit(n)
//                .peek(i -> slowDown()) // forEach랑 비슷함. forEach(최종연산) 는 return 후 끝나버리는데 peek() (중개연산) 는 return X // slowDown() 메서드는 void 타입이라 딱 맞음.
                .reduce(Long::sum)
                .get();
    }
    public static long parallelSum(long n) {
        return Stream
                .iterate(1L, i -> i + 1)
                .limit(n)
                .parallel()
//                .peek(i -> slowDown())
                .reduce(Long::sum)
                .get();
    }
    public static long rangedSum(long n) {
        return LongStream
                .rangeClosed(1, n)
//                .peek(i -> slowDown())
                .reduce(Long::sum)
                .getAsLong();
    }
    public static long parallelRangedSum(long n) {
        return LongStream
                .rangeClosed(1, n)
                .parallel()
//                .peek(i -> slowDown())
                .reduce(Long::sum)
                .getAsLong();
    }
```
우선 저 peek() 함수 안에 있는 slowDown() 이라는 메서드는 무시하고 보자면, 총 5개의 메서드들이 있는데,

1. iterativeSum ? 일반적인 for문으로, 우리는 밑에 n 값을 주고 그 n 값까지 다 더할 예정이다. 나머지 4개의 메서드도 동일
2. sequentialSum ? iterate 함수를 사용한다. 이 함수의 특징은 처음 1L값이 있으며, i에 들어가게 되고 그 i는 뒤의 i + 1 연산에 들어가 2L이 되고
그 2L이 다시 i값으로 들어간다. 이게 계속해서 반복이 되는데... 여기서 알아볼 수 있는 특징은 전의 값(i)이 있어야 계산을 할 수 있다는 점이다.
3. parallelSum ? 마찬가지로 2번과 같지만 parallel() 함수가 들어가 있다.
4. rangedSum ? range(rangedClose)를 써서 연산을 한다.
5. parallelRangedSum ? 마찬가지로 4번과 같지만 parallel() 함수가 들어가 있다.

main 메서드 안에서 이제 n값을 주고 각각 메서드들 마다의 속도차이를 체크해 보자.

```java
final long n = 1_000_000; // 1_000
        final long start1 = System.currentTimeMillis();
        System.out.println("     iterativeSum(n): " + iterativeSum(n));
        System.out.println("                      " + (System.currentTimeMillis() - start1) + " ms\n");
```

1번 메서드(iterativeSum) 와 모두 동일하다. 그렇게 run 했을 경우 결과는 어떻게 나올까?

```
     iterativeSum(n): 500000500000
                      21 ms
    sequentialSum(n): 500000500000
                      48 ms
      parallelSum(n): 500000500000
                      109 ms
        rangedSum(n): 500000500000
                      10 ms
parallelRangedSum(n): 500000500000
                      45 ms
```

단순 연산 iterativeSum, rangedSum 이 가장 빠르며 그 다음으로 전의 값을 필요로 하는 iterate함수 2번, 그 뒤로 parallel()
함수를 사용한 5번, 3번 순으로 느리다. parallel() 함수는 병렬처리임에도 왜 가장 느릴까? 병렬처리라 함은 분명 계산들을 
각각 계산하고 합해서 계산할 텐데 당연히 빨라야 되지 않을까 ? 싶지만 아니다.

해당 3번의 이유는 이렇다. 이것은 내가 따로 주석으로 작성한 내용을 그대로 복사해왔다.

```
// start3 (parallelSum) 느린 이유...?
/**
 * .iterate(1L, i -> i + 1) : 1L 부터 시작하여 처음 i에 1L가 들어가고, 연산(i + 1)을 하여 2L로 만들고... 반복.
 *  parallel로 만들면, 1, 2, 3, 4 ... 이것들을 쪼개는 방식(병렬)으로 되는데... .iterate(1L, i -> i + 1) 로직에서,
 *  이전값이 있어야만 다음 연산을 할 수 있음. 근데 다른 코어(thread)들이 자기도 계산을 하려 값을 찾아봤는데 못찾음. (아직 안 들어와서)
 *  1,      2,        3,        4 ... ... n-1       n       n의 값
 *  |       |         |         |   ...    |        |       스레드
 *  계산    계산       ..                   ?(전값 못찾음)
 *  못찾아서 스레드들은 기다리게 되고 그만큼 느려지는 것. 그래서 해당 로직에서는 느려질 수밖에 없음 (parallel()을 쓴다고 무조건 느린 건 아님)
 *  --------
 */
```

5번 parallel() 함수를 써서 느려지지 않았던가? 해당 메서드의 느려짐에도 이유가 따로 있다.

```
// rangeClosed()에서도..
/**
 * 1부터 n(해당 예제에서는 100만)까지 다 저장해놓는 것이 아님.
 * 1부터 100만 까지 예를 들어, 코어가 4개면 4등분해서 각각 계산 후 합치는 것이 아니라 그 안에서 무수히(적당히) 나뉘어서 계산하다가
 * 합치고, 또 나눠서 합치고.. 이런 과정이 계속해서 반복이 되는 것. 근데 순차적으로 합치는 게 아님. 스레드들이 어느정도 나눠서
 * 예를 들어 뭐.. 1 ~ 50까지 더한 값과 151 ~ 200까지 더한 값을 먼저 합치고 그런.. 그렇게 지지고 볶고 합쳐서 최종적인 결과가 나옴.
 * 결국 순서는 보장이 안 된다는 말과 같음.
 * 쪼개고 합치고 쪼개고 합치고는 computing power 를 낭비하기 쉬움. 그래서 시간이 더 걸리는 것 ㅇㅇ (물론 start3보단 아님)
 *
 * 여기서 효율적으로 parallel()을 쓰고 싶다면 중개연산으로 map()이나 filter() 등등.. 쓰게 되면 빛을 보게 됨.
 *  map()이나 filter() ...이들은 계산하는 데에 시간이 오래 걸리기 때문에
 **/
```

그렇다면 중개연산을 넣어서 계산해보면 결과는 어떻게 나올까 ? 먼저, 메서드를 만들어 보자.
```java
private static void slowDown() {
        try {
            TimeUnit.MILLISECONDS.sleep(10L);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
```

10초간의 텀을 주는 메서드를 만들었고, 앞에서 봤듯이 이를 peek()함수로 모두 넣어줄 예정이다. 주석을 해제하고, 
n의 값은 100만이 아닌 1000으로 줄여서 확인해 보자.

결과는 ?

```
     iterativeSum(n): 500500
                      15687 ms
    sequentialSum(n): 500500
                      15540 ms
      parallelSum(n): 500500
                      1485 ms
        rangedSum(n): 500500
                      15582 ms
parallelRangedSum(n): 500500
                      1463 ms
```

굉장한 속도차이다. 병렬처리의 차이는 거의 10배를 보여준다. peek() function 의 속도가 느리다 보니 위에서 이야기했듯이 중개연산을
병렬처리함으로써 연산의 속도가 차이가 나는 것이다.

그래서 이 예제들을 통해서 깨달은 바로는 병렬처리라고 무조건 효율이 좋다는 것은 아니라는 것. 때문에 적절한 상황에서
parallel 처리하도록 하는 것이 중요하다.
