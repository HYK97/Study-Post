필자가 Java를 공부하는 데에 계속 의문이 들었다. 🤔
강사님께서 쓰신 코드가 .java 파일로 업로드가 되어 있을 텐데... 아 ! 찾아보니 있었다 !!
하지만 백문이불여일타- 라고 했던가. 있음에도 불구하고 Java 기초인 필자는 영타라도 늘려보자는
마음에 손으로 직접 모든 것을 쳐 내려가던 와중 든 생각
>이대로 가다간 강의도 못듣고 시간도 두 세배는 걸릴 것이고 받아쓰기만 될 것 같다!!

물론 어떻게 코드를 작성하고 실행되는지는 당연히 봐야하나, 준비된 코드에 새로운 class, 메서드, 등등.. 작성하는 것은 따로 쓰셨기에 준비된 .java 파일로 거기에 필자가 따라 쓰는 게 더더욱 학습 효과에 좋을 거 같다고 판단했다.

그렇게 받은 .java 파일. 실행하려고 IDE까지 키고 .java 코드들을 봤다. (코드 내용은 문제가 될 거 같기에 가립니다.)

![](https://velog.velcdn.com/images/yeb0/post/756a0292-c081-4a48-91bf-a0d78c50bc05/image.png)
좋다 ! 아주 잘 볼 수 있고 덧붙여서, 그 위에 코드도 같이 작성할 수 있게 됐다. 이제 Run을 해보려 하는데..![](https://velog.velcdn.com/images/yeb0/post/20b65d98-5947-4cd6-9088-225d3ffb6a5b/image.png)
??? 처음 보는 팝업창이다. 일단 RUN을 눌러 보았는데..
![](https://velog.velcdn.com/images/yeb0/post/121c0450-ef63-42ef-9a6a-c99f9786300c/image.png)
## java.lang.ClassNotFoundException
혼자 공부하는 데에 처음 마주한 에러.

class를 찾을 수 없다니.. 원인이 바로 나타나 있으니 알아보자.

구글링을 통해 알아본 결과 여러가지들의 해결방법들이 있었다.
Project - Clean을 통해 해결하는 방법, 해당 프로젝트에 우클릭을 하여 Properties > Java Build Path > Order And Export로 들어가 JRE System Library를 체크... ... 등 많은 방법들이 있었고, 다 해봤다.

되지 않는다!!! 단순히 나는 외부의 .java 파일을 실행하려고 하는 것일 뿐.. 일어난 원인의 상황이 다르다 생각했기에 조금 더 구글링을 자세히 해보았다. 결과...

![](https://velog.velcdn.com/images/yeb0/post/d7c9b9ae-8dc7-43f6-a9a4-f7a08700d21f/image.png)

헉!!! 생겼다. run 버튼! 눌러봐야겠다!!!!!!!!!!! 당장 눌러보자.
![](https://velog.velcdn.com/images/yeb0/post/d09ce50f-1d81-46ea-8339-f8a1bab87921/image.png)
다시 똑같은 오류다. 어딘가 문제가 있는 거 같은데... 필자가 사용했던 방법은 이렇다.
Project Structure에서 classPath 추가하는 방법이다.

Modules 선택 후 오른쪽에 Dependencies 탭에 등록되어 있는 라이브러리 중 JARs 파일을 등록했다.
등록해서 연결했더니 위의 상황인 것이다.

다시 살펴보니 왼쪽 Project란에 나와있지도 않았다. Package에 들어가지 않았다는 말인 거 같다. 오케이. Modules에 들어가면 해결이 될 거 같다.

Project Structure에 들어가 Settings > Modules 란에 + 버튼을 눌러 import Module을 눌렀다.
![](https://velog.velcdn.com/images/yeb0/post/385c7acc-f0a3-4042-918f-81d6c71a11cf/image.png)

Select Flie or Directory to Import 팝업이 뜨면서
필자가 원하는 Directory를 선택하여 OK버튼을 누르고 코드가 작성되어 있는 .Java 파일들을 모두 확인하여 선택 후 확인한 결과!!!![](https://velog.velcdn.com/images/yeb0/post/5f3590da-cc31-48ea-94dd-e94e10035a8e/image.png)

아주 잘 된다 !! 여러 문제가 아닌 단순히 해당 파일들을 import 해주면 됐던 문제였다. 잠시 몇 시간 골머리를 앓았는데 해결 되어 완치됐다. 이번 오류를 통해 classpath가 무엇인지 같이 공부하게 된 계기가 되어 좋았다. 나중에 협업할 때에도 이런 경험을 토대로 헤쳐나가면 되지 않을까 싶다.

Run버튼이 나왔을 때의 그 희열감을 가지게 해준 분께도 감사함을 전한다.