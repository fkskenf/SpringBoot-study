# SpringBoot-study

### spec
> Java 1.8 <br>
> Spring Boot 2.7.1 <br>
> Gradle 7.5 <br>

### reference
> [IntelliJ Guide](https://github.com/fkskenf/IDE_setting/blob/master/IntelliJ%20Guide(Mac%20OS)/Intellij%20Guid.md)

### check
> 1. Endpoint 변경 : build.gradle에서 수정
```java
bootJar {
    archiveFileName.set("common.jar")
}
```

# 1. @SpringBootApplication 속성과 구성요소

> proxyBeanMethods()
>> 1. @Bean으로 빈을 등록하는 메소드에 프록시 패턴을 적용할 것인지를 결정하는 속성이다. <br>
>> 2. 기본값은 true이며, 별다른 설정을 하지 않았다면 @Bean 메소드에 프록시가 기본으로 적용된다. <br>
>> 3 .항상 싱글톤 스코프를 강제하여 1개의 객체만을 생성하기 위함

<br>

> 1. @SpringBootConfiguration
>> 1. @Configuration의 하위 어노테이션으로 @Configuration과 동일한 역할을 수행 <br>
>> 2. 해당 어노테이션을 기준으로 설정들을 불러오기 위함 <br>

<br>

> 2. @EnableAutoConfiguration
>> 필요할 것 같은 빈들을 자동으로 설정해주는 자동 설정 기능을 활성화한다.

<br>

> 3. @ComponentScan
>> 1. 빈을 등록하기 위한 어노테이션들을 탐색하는 위치를 지정 
>> 2. 스프링 부트의 메인 클래스는 루트 패키지
>> 3. includeFilters : 해당 클래스들을 컴포넌트 스캔 대상에 포함시킴
>> 4. excludeFilters : 해당 클래스들을 컴포넌트 스캔 대상에서 제외시킴

# 2. SpringBootApplication의 생성과 초기화 과정

> 1. SpringBootApplication run 메소드 호출
>> run 내부에서 ApplicaitonContext를 만들어 실행하고 반환

> 2. SpringBootApplication 초기화 및 실행 과정
>> Class primarySources는 애플리케이션의 메인 클래스이며 전달된 메인 클래스가 null이면 에러를 반환 <br>
> - primarySources 클래스가 null인지 검사 후 과정 5가지
>>> 1. 클래스 패스로부터 애플리케이션 타입을 추론함 : ApplicationContext나 Environment의 구현체를 만드는데 사용
>>>> 1. 웹이 아닌 애플리케이션: AnnotationConfigApplicationContext <br>
>>>> 2. 서블릿 기반의 웹 애플리케이션: AnnotationConfigServletWebServerApplicationContext <br>
>>>> 3. 리액티브 웹 애플리케이션: AnnotationConfigReactiveWebServerApplicationContext <br>
>>> 2. BootstrapRegistryInitializer를 불러오고 셋해줌 <br>
>>>> 1. 임시 컨텍스트 객체 <br>
>>>> 2. initializer들을 SpringFactory에서 가져오고 객체로 만들어 셋하는 작업을 진행 <br>
>>>> 3. loadFactoryNames 메소드 : 객체로 만들 클래스를 SpringFactory에서 조회 <br>
>>>> 4. 연속적인 File I/O로 인한 성능 문제를 방지하기 위해 캐싱을 적용 <br>
>>> 3. ApplicationContextInitializer를 찾아서 셋해줌
>>>> 1. 이미 BootStrap 단계에서 spring.factories를 스캔했으므로 파일에서 찾는 것이 아닌 캐싱된 값으로부터 찾는다 <br>
>>>> 2. spring.factories에서 읽은 값들의 key로 ApplicationContextInitializer에 해당하는 Value들만을 조회해 객체로 가져온다 <br>
>>>> 3. SpringBoot는 해당 구현체들을 생성한 다음에 Initializer값을 셋 <br>
>>> 4. ApplicationListener를 찾아서 셋해줌
>>>> 1. ApplicationListener들을 불러오고 listener 값을 셋 <br>
>>> 5. 메인클래스를 추론함
>>>> 1. SpringBoot는 먼저 의도적으로 RuntimeException을 발생시킨 다음에 해당 StackTrace를 가져온다<br>
>>>> 2. 그리고 "main"이 붙어있는 메소드를 찾으면 Main 클래스라고 판단하고 해당 클래스의 이름을 찾는다<br>

# 3. SpringBootApplication의 run 메소드와 실행과정
> 1. StopWatch로 실행 시간 측정 시작
>> SpringBoot를 실행하면 다음과 같이 실행 시간이 나오는데, run 안에서 가장 먼저 측정을 시작함을 확인할 수 있다. <br>
> 2. BootStrapContext 생성
>> 애플리케이션 컨텍스트가 준비될 때 까지 환경 변수들을 관리하는 스프링의 Environment 객체를 후처리하기 위한 임시 컨텍스트 <br>
> 3. Java AWT Headless Property 설정
>> 모니터나 마우스, 키보드 등의 디스플레이 장치가 없는 서버 환경에서 UI 클래스를 사용할 수 있도록 하는 옵션 <br>
> 4. 스프링 애플리케이션 리스너 조회 및 starting 처리
>> 1. 애플리케이션 컨텍스트를 준비할 때 호출되어야하는 리스너들을 찾아서 BootStrapContext의 리스너로써 실행 <br>
>> 2. 생성 시간이 긴 객체들의 경우 객체 생성을 위한 리스너를 만들어 등록하면 BootStrapContext가 애플리케이션 컨텍스트를 준비함과 동시에 객체를 생성하도록 함으로써 Lazy하게 접근가능 <br>
> 5. Arguments 래핑 및 Environment 준비
>> 1. String[] 형태의 인자를 스프링 부트를 위한 인자인 ApplicationArguments로 래핑 <br>
>> 2. 애플리케이션 타입에 맞게 Environment 구현체를 생성 <br>
>> 3. 다음에 만들어진 Environment에 Property나 Profile 등과 같은 값들을 셋팅해주고 SpringApplication에 바인딩 <br>
> 6. IgnoreBeanInfo 설정
>> 1.  오늘날 더이상 자바 빈즈를 사용하지 않으므로 자바 빈즈 작업을 처리하지 않도록 설정해주는 부분 <br>
> 7. 배너 출력
>> 배너 수정은 Generator로 배너를 만들고 resources 하위에 banner.txt를 넣어주면 된다 <br>
> 8. 애플리케이션 컨텍스트 생성
>> 1. 애플리케이션 컨텍스트를 생성하는 부분 <br> 
>> 2. 애플리케이션 컨텍스트 객체의 생성은 팩토리로 위임 <br>
> 9. Context 준비 단계
>> Context가 생성된 후에 해주어야 하는 후처리 작업들과 빈들을 등록하는 refresh 단계를 위한 전처리 작업 등이 수행 <br> 
>> 1. Environment를 애플리케이션 컨텍스트에 설정 <br>
>> 2. beanNameGenarator(빈 이름 지정 클래스), resourceLoader(리소스를 불러오는 클래스), conversionService(프로퍼티의 타입 변환) 등이 생성되었으면 싱글톤 빈으로 등록 <br>
>> 3. 앞선 SpringApplication 생성 단계에서 찾았던 initializer들을 initialize 해주는 등의 작업 <br>
>> (물론 생성자 주입일 경우에만 실행 시점에 발생하고, @Autowired 필드 주입인 경우에는 호출 시에 발생한다. 그러므로 우리는 생성자 주입을 사용해야 한다.) <br>
>> 마찬가지로 동일한 이름의 빈이 여러 개 있을 경우에도 에러가 발생하고 애플리케이션이 종료된다. <br>
> 10. Context Refresh 단계
>> 1. refreshContext() 메소드에서는 우리가 만든 빈들을 찾아서 등록하고 웹서버를 만들어 실행하는 등의 핵심 작업들이 진행 <br>
>> 2. 모든 객체들이 싱글톤으로 인스턴스화되는데, 만약 에러가 발생하면 등록된 모든 빈들을 제거 <br>
> 11. Context Refresh 후처리 단계
> 12. 실행 시간 출력 및 리스너 started 처리
>> 애플리케이션을 시작하는데 걸린 시간을 로그로 남기고 리스너들을 started 처리 <br>
> 13. Runners 실행
>> 애플리케이션이 실행된 이후에 초기화 작업을 필요로 하는 경우가 있다. 그럴때 사용할 수 있는 선택지 중 하나가 Runner를 등록 <br>
>> 1. Runner에는 총 2가지가 존재하는데, String을 파라미터로 필요로하는 넘기는 경우에는 CommandLineRunner를 <br>
>> 2. 다른 타입을 파라미터로 필요로 하는 경우에는 ApplicationRunner를 사용 <br>
>> Runner를 구현하게 하고, 빈으로 등록하면 callRunner에서는 CommandLineRunner과 ApplicationRunner 구현체들을 찾아서 run 메소드를 호출 <br>

# 4. 애플리케이션 컨텍스트(ApplicationContext)의 refreshContext 동작 과정
> - refreshContext 동작 과정 
>> 먼저 ShutdownHook을 등록 : ShutdownHook이란 프로그램의 종료를 감지하여 프로그램 종료 시에 후처리 작업을 진행하는 기술 <br>
>> 프로그램이 실행되다가 프로세스가 죽어버리면 연결 종료, 자원 반납 등의 처리를 못하게 되는데, ShutdownHook을 사용함으로써 프로그램이 종료되어도 별도의 쓰레드가 올바른 프로그램 종료(Graceful Shutdown)를 위한 작업을 처리할 수 있다. <br>

> - refresh는 start-up 메소드로써 싱글톤 빈으로 등록할 클래스들을 찾아서 생성하고 후처리하는 단계 <br>
>> 1. refresh 준비 단계 : 애플리케이션 컨텍스트의 상태를 active로 변경하는 등의 준비 작업<br>
>> : 빈 팩토리에서 빈을 꺼내는 작업은 active 상태가 true 일때만 가능
>> 2. BeanFactory 준비 단계 <br>
>> : beanFactory가 동작하기 위한 준비 작업들이 진행
>> 3. BeanFactory의 후처리 진행 <br>
>> 4. BeanFactoryPostProcessor 실행 <br>
>> : 빈을 탐색하는 것처럼 빈 팩토리가 준비된 후에 해야하는 후처리기들을 실행 (싱글톤 객체로 인스턴스화할 빈을 탐색 등등...) <br>
>> : BeanFactory가 준비되고 빈을 인스턴스화하기 전의 중간 단계로써 빈의 목록을 불러오고, 불러온 빈의 메타 정보를 조작하기 위한 BeanFactoryPostProcessor를 객체로 만들어 실행
>> 5. BeanPostProcessor 등록 <br>
>> : 빈들이 생성되고 나서 빈의 내용이나 빈 자체를 변경하기 위한 빈 후처리기인 BeanPostProcessor를 등록
>> 6. MessageSource 및 Event Multicaster 초기화 <br>
>> : 다국어 처리를 위한 MessageSource와 ApplicationListener에 event를 publish하기 위한 Event Multicaster를 초기화하고 있다.
>> 7. onRefresh(웹 서버 생성 및 실행) <br>
>> 8. ApplicationListener 조회 및 등록 <br>
>> : ApplicationListener의 구현체들을 찾아서 EventMultiCaster에 등록
>> 9. 빈들의 인스턴스화 및 후처리 <br>
>> 10. refresh 마무리 단계 <br>
>> : 애플리케이션 컨텍스트를 준비하기 위해 사용되었던 resourceCache를 제거하고, Lifecycle Processor를 초기화하여 refresh를 전파하고, 최종 이벤트를 전파하며 마무리된다. <br>
>> Lifecycle Processor에는 웹서버와 관련된 부분이 있어서 refresh가 전파되면, 웹서버가 실행된다 <br>
>> 추가) refreshContext가 실패한 경우 마무리 단계 <br>

# 5. DispatcherServlet(디스패처 서블릿) 동작 과정
> 1. 디스패처 서블릿은 모든 요청을 가장 먼저 받는 프론트 컨트롤러
>> 1. 서블리 요청/응답을 HTTP 서블릿 요청/응답으로 변환
>> : Servlet 관련 Request/Response 객체를 Http 관련 Request/Response로 캐스팅
>> 2. Http Method에 따른 처리 작업 진행
>> 3. 요청에 대한 공통 처리 작업 진행
>> 4. 컨트롤러로 요청을 위임
>>> 1. 요청에 패핑되는 HandlerMapping (HandlerExecutionChain) 조회 <br>
>>> :HandlerMethod에는 매핑되는 컨트롤러의 메소드와 컨트롤러 빈 이름(또는 컨트롤러 빈) 및 빈 팩토리 등이 저장
>>> 2. 요청을 처리할 HandlerAdapter 조회 <br>
>>> 3. HandlerAdapter를 통해 컨트롤러 메소드 호출(HandlerExecutionChain 처리) <br>
>>> : 컨트롤러 메소드 호출 전에는 적합한 파라미터를 만들어 넣어주어야 하며(ArgumentResolver), 호출 후에는 메세지 컨버터를 통해 ResponseEntity의 Body를 찾아 Json 직렬화하는 등(ReturnValueHandler)이 필요
> 2. DispatcherServlet(디스패처 서블릿)의 초기화 과정
>> 1. 초기화: 요청이 들어오면 서블릿이 웹 컨테이너에 등록되어 있는지 확인하고, 없으면 초기화를 진행함 <br>
>> 2. 요청 처리: 요청이 들어오면 각각의 HTTP 메소드에 맞게 요청을 처리함 <br>
>> 3. 소멸: 웹 컨테이너가 서블릿에 종료 요청을 하여 종료 시에 처리해야하는 작업들을 처리함 <br>



