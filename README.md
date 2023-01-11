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
