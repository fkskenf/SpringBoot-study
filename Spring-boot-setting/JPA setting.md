# 1. build.gradle Setting
```java
plugins {
	id 'java' // init
	id 'org.springfra mework.boot' version '2.7.7' // init
	id 'io.spring.dependency-management' version '1.0.15.RELEASE' // init
}

group = 'com.blog' // init
version = '0.0.1-SNAPSHOT' // init
sourceCompatibility = '17' // init

configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa' // JPA
	implementation 'org.springframework.boot:spring-boot-starter-validation'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.7.1' // DB value view
	compileOnly 'org.projectlombok:lombok'
	compileOnly 'mysql:mysql-connector-java' //DB mysql
	annotationProcessor 'org.projectlombok:lombok'
}

test {
	useJUnitPlatform()
}


```

# 2. application.yml Setting
```java

spring:
  datasource:
    url: jdbc:mysql://127.0.0.1:3306/sys?serverTimezone=UTC&autoReconnect=true&useSSL=false
    username: root
    password: ses8178@
    driver-class-name: com.mysql.cj.jdbc.Driver
  jpa:
    database: mysql
    show-sql: true
    open-in-view: false
    hibernate:
      ddl-auto: validate
    properties:
      hibernate:
        format_sql: true
        show_sql: true

logging:
  level:
    org:
      apache:
        coyote:
          http11: debug

      hiberante:
        SQL: debug
        type:
          descriptor:
            sql: trace
            
    boardexample:
      myboard: info
```

# 3. Controller Setting
- check
> 1. position
> 2. Aunotation : RestController, GetMapping...
```java
package com.blog.demo.controller;

import com.blog.demo.domain.board;
import lombok.RequiredArgsConstructor;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import com.blog.demo.repository.boardQueryRepository;

@RequiredArgsConstructor
@RestController
public class HellowController {
    private final boardQueryRepository boardQueryRepository;

    @GetMapping("/insert")
    public ResponseEntity<?> get() {
        board board2 = board.builder()
                .code(4)
                .title("title")
                .content("content")
                .writer("")
                .build();
        boardQueryRepository.save(board2);
        return ResponseEntity.ok().body("");
    }
}
```

# 4. Entity Setting
- check
> 1. position
> 2. Aunotation 
> 3. Mapping : Entity & Table
>> Type : NOT NULL, Auto Increment...
```java
package com.blog.demo.domain;

import lombok.*;
import org.hibernate.annotations.DynamicInsert;
import org.hibernate.annotations.DynamicUpdate;

import javax.persistence.*;
import java.time.LocalDateTime;

@Entity
@Table(schema = "sys", name = "board")
@DynamicInsert
@DynamicUpdate
@Getter
@Builder
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@AllArgsConstructor(access = AccessLevel.PRIVATE)
public class board {

    @Id
//    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "code", updatable = false, unique = true, nullable = false)
    private Integer code;

    @Column(nullable = false)
    private String title;

    @Column(nullable = false)
    private String content;

    @Column(nullable = false)
    private String writer;

    private LocalDateTime reg_datetime;

    @Column(nullable = false)
    private String boardcol;
}
```

# 5. Repository Setting
> 1. Interface Setting 
> - check
>> 1. 상속 : extends JpaRepository<적용Entity, 타입>
```java
package com.blog.demo.repository;

import com.blog.demo.domain.board;
import org.springframework.data.jpa.repository.JpaRepository;

public interface boardRepository extends JpaRepository<board, Long> {
}
```
> 2. Class : serviceQueryRepository Setting 
> - check
>> 1. Aunotation : @Repository, @RequiredArgsConstructor
>> 2. ID : Interface 
```java
package com.blog.demo.repository;

import com.blog.demo.domain.board;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Repository;

@Repository
@RequiredArgsConstructor
public class boardQueryRepository {

    private final boardRepository boardRepository;
    public board save(board board) {
        return boardRepository.save(board);
    }
}
```

