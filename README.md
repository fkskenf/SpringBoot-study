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

# @SpringBootApplication 속성과 구성요소

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

# SpringBootApplication의 생성과 초기화 과정

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
