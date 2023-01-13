- reference : https://ttl-blog.tistory.com/266

# Spring Boot Project 생성하는 법

> 1. 프로젝트 생성 : https://start.spring.io/
>> 1. Project : Gradle - Groovy <br>
>> 2. Language : Java <br>
>> 3. Spring Boot : 2.7.7 <br>
>> 4. Project Metadata : 작성 <br>
>> 5. Packaging : Jar <br>
>> 6. Java : 17 <br>
> 7. GENERATE 클릭 <br>

> 2. 프로젝트 IntelliJ Import 하기
>> 1. IntelliJ 접속
>> 2. File > New > Project from Existing Sources... > 생성한 프로젝트 선택
>> 3. build.gradle 추가
>> 4. application.yml : 이름변경 후 적용
>> 5. Preperence 설정 수정 : reference 참고


# Error 대처
> 1. application.yml : datasource, jpa 설정 전까지는 주석 처리
> 2. SpringApplication.run(DemoApplication.class, args); 에러시 : import 잘 하기
