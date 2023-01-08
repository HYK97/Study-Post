# Annotation 정리

## Java Annotation
  자바 애노테이션은 소스코드에 추가해서 사용할 수 있는 메타 데이터의 일종이다.
  메타 데이터란 애플리케이션이 처리해야 할 데이터가 아니라 컴파일 과정과 실행 과정에서 코드를 어떻게 처리해야 하는지를
  알려주기 위한 추가 정보다.(애노테이션을 사용해야 하는 이유) 자바의 애노테이션은 @기호를 앞에 붙여서 사용한다.
  자바 애노테이션은 클래스 파일에 임베드되어 컴파일러에 의해 생성된 이후 JVM에 포함되어 동작한다.
  Spring에서 제공되는 대부분의 애노테이션은 런타임 시 특정 기능을 실행하도록 정보를 제공하는 용도로 사용되고 있다.

## 자바 애노테이션의 기능
- 컴파일러에게 코드 작성 문법에러를 체크하도록 정보 제공
- 소프트웨어 개발 환경이 빌드나 배포 시 코드를 자동으로 생성할 수 있도록 정보 제공
- 런타임에 특정 기능을 실행하도록 정보를 제공

## 내장 애노테이션
자바에서 제공하는 내장된 애노테이션

## 대표적인 내장 애노테이션
  @Override
- 선언한 메서드가 오버라이드 되었다는 것을 나타낸다.
- 만약 상위(부모) 클래스 또는 인터페이스에서 해당 메서드를 찾을 수 없다면 컴파일 에러를 발생시킨다.

@Deprecated
- 해당 메서드가 더 이상 사용되지 않음을 표시한다.
- 만약 사용할 경우 컴파일 경고를 발생시킨다.

....

## 애노테이션 파일 정의
1. Target(애노테이션의 적용 대상)
2. Retention(애노테이션이 유지되는 대상)
3. Annotation Name(class가 아닌 @interface로 정의된 애노테이션 이름)

## Target 설정 - ElementType Enum의 값
- ANNOTATION TYPE : 애노테이션 선언
- CONSTRUCTOR : 생성자 선언
- FIELD : 필드 선언(열거형 상수 포함)
- LOCAL VARIABLE : 지역 변수 선언
- METHOD : 메서드 선언
- PACKAGE : 패키지 선언
- PARAMETER : public parameter
- TYPE : 클래스, 인터페이스(주석 유형 포함) 또는 열거형 선언
- TYPE PARAMETER : Type 파라미터 선언
- TYPE USE : Type이 사용되는 곳

## Retention 설정 - RetentionPolicy Enum의 값
- CLASS : 컴파일러에 의해 클래스 파일에 기록되지만 런타임에는 유지되지 않는다.
- RUNTIME : 컴파일러에 의해 클래스 파일에 기록되고 런타임에 유지된다.
- SOURCE : 소스에만 반영되어 컴파일러에 의해 삭제된다.

## Spring @Controller 어노테이션 분석 -> Controller.class 파일
```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Controller {
    @AliasFor(annotation = Component.class)
    String value() default "";
}
```

우선 @Target은 ElementType.TYPE으로 정의되어 우리가 흔히 Controller 클래스 파일을 만들었을 때
해당 클래스에 어노테이션을 붙일 수 있게 해두었다. 그리고 Retention은 런타임 시에도 유지되어야 하므로
RetentionPolicy.RUNTIME으로 해둔 것을 볼 수 있다. @Documented는 JavaDoc을 사용할 경우 JavaDoc에 자동으로 Document를 남겨주는 애노테이션
그리고 Controller에서 @Component 애노테이션을 붙이고 @AliasFor로 Component Scan에서 Controller도 Component로 인식할 수 있도록 해두었다.

## JavaDoc이란?
  문서화를 위한 주석으로, 형식을 강제할 수 있고 IDE 지원이 가능하다는 특징이 있어 원칙을 갖고 작성한다.

* JavaDoc의 원칙
1. 리턴값을 반드시 설명한다.
2. 예외 클래스라면 어떤 경우에 던지는지 설명한다.
3. 구현에 의존하지 않는다.

ex) 나쁜 예
```java
/**
* 카운트를 1씩 증가시키는 메서드
 **/
public void increaseCount() {
  this.count += 1L;
}
```

만약 해당 코드에서 카운트를 2씩 증가시킨다면 주석도 바꿔줘야 하므로 좋은 예가 아니다.

ex) 좋은 예
```java
/**
* 카운트를 증가시키는 메서드
 **/
public void increaseCount() {
  this.count += 1L;
}
```

위의 코드처럼 메소드의 책임과 역할만 짧게 설명하는 것이 좋다.

 