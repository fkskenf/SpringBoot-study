# SpringBoot-study

Inflearn : 스프링부트 개념정리(이론)

## Spring이란
1. Spring은 FrameWork이다.
> 1. 틀(Frame)에서 동작(Work)한다
> 2. 주니어 개발자도 Frame에 맞춰 개발하면 쉽게 개발할 수 있다.
2. Spring은 Open Source이다.
> 1. 누구나 사용할 수 있다.
> 2. 내부소스를 분석하여, 기여(Contribute)할 수 있다.
3. Spring은 IOC Container를 가진다.
> 1. IOC(역전 제어) : 주도권이 Spring에 있다.
>> 1. Class : 설계도
>>> 1. Class : Object 가능
>>> 2. Abstract Class : Object 불가능
>> 2. Object : 실체화가 가능한 것 (추상적인 것)
>> 3. Instance : 실체화가 된것
* 예시 : 가구(추상 Class) > 의자, 침대 (Object) > 실체화(Instance)

```java
// Heap 메모리 영역에 s 가 올라간다.
public void make(){ 
  의자 s = new 의자();
} 
```
```java
// Heap 메모리 영역에 새로운 s 가 추가로 올라간다.
public void use(){ 
  의자 s = new 의자();
} 
```
두 의자(s)는 서로 다르므로, 공유할 수 없다. 그래서 Spring이 각 Object들을 미리 Scan해서 Heap 메모리 영역에 올린다.

이렇게 객체들을 사용자가 아닌 Spring이 주도권을 가지고 있어 IOC 이다.
<br></br>
<br></br>
4. Spring은 DI를 지원한다.
> 1. DI(의존 주입) : IOC로 Scan된 객체들을 주입하여, 사용하는것을 DI라고 한다.
5. Spring은 많은 Filter를 가지고 있다.
> 1. Filter는 문지기와 같은 역할을 한다.
> 2. (Filter[web.xml])Tomcat -> (Interceptor[AOP])Spring container
6. Spring은 많은 Aunotation을 가지고 있다.(리플렉션, 컴파일체킹)
> 1. 컴파일체킹(Compile Checking) : 어노테이션을 컴파일러가 체킹한다. (에러 잡아준다.)
> 2. Aunotation : 객체 생성
>> 1. @Component : 클래스 메모리에 로딩
>> 2. @Autowired : 로딩된 객체를 해당 변수에 집어 넣기
> 3. 리플렉션(Reflection) : 분석 기법 (RunTime에 분석한다.)
>> 1. Method, Field, Aunotation을 체킹하고, 일부 역할(DI 등등)을 수행한다.
7. Spring은 MessageConverter를 가지고 있다. 기본값은 현재 Json이다.
> 1. java 프로그램을 Json으로 변경해준다. (요청,응답 둘다)
> 2. Spring Library 지원 : Jackson
8. Spring은 BufferReader와 BufferWriter를 쉽게 사용할 수 있다.
> 1. Aunotation으로 제공 : @RequestBody, @ResponseBody
> 2. Bit단위로 통신 (0,1,0,1,1,1)를 영어 한문자(8bit)로 했으면 좋겠다.
>> 8bit씩 끊어 읽어. => 1 Byte (통신단위)<br></br>
>> 언어별 통신을 위해 유니코드 (3byte 통신) 사용
> 5. 통신 : Byte Stream -> InputStreamReader (문자 하나로 읽어줌/ 배열) 
>> 배열의 크기를 정해야하기에 낭비가 있음
> 6. 그리하여 BufferReader(가변길이의 문자를 받을 수 있음)사용
>> BufferWriter 보다 PrintWriter 많이 사용
9. Spring은 계속 발전중이다.
