## MySQL 설치
1. 직접 다운 받아서 설치
2. 도커로 설치
```Terminer
docker create --name mysql8 -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 mysql:8.0.27

docker start mysql8
```

1. 데이터베이스, DB 사용자, 권한
```query
create database jpabegin CHARACTER SET utf8mb4;

CREATE USER 'jpauser'@'localhost' IDENTIFIED BY 'jpapass';
CREATE USER 'jpauser'@'%' IDENTIFIED BY 'jpapass';

GRANT ALL PRIVILEGES ON jpabegin.* TO 'jpauser'@'localhost';
GRANT ALL PRIVILEGES ON jpabegin.* TO 'jpauser'@'%';
```
2. 테이블 생성
3. 자바 프로젝트 생성
> 1. 메이븐 프로젝트
> 2. JDK 17
> 3. 프로젝트 이름 : jpa-01

4. pom.xml
```xml
<hiberate.version> 6.0.0.Final </>
  
<dependency>
  hibernate-core
  hibernate-hikaricp // 커넥션풀을 담당
  mysql-connectoer-java
  logback-classic
</>
```

5. persistence.xml (src/main/resources/META-INF) > spring/springBoot가 알아서 생성해줌
```xml
<class> : 매핑 클래스
jakarta.persistence.jdbc.*** : DB 연결 설정
hibernate.dialect : 하이버네이트 DB종류 설정
hibernate.hikar.*** :  커넥션 풀 설정
```

6. User Class
> 1. @Entity : DB테이블과 매핑 대상
> 2. @Table(name = "user") : user테이블과 매핑
> 3. @Id : 식별자 대응
> 4. @Column(name = "create_date") : create_date 컬럼과 매핑  

* 영속 컨텍스트
> 응용프로그램(엔티티 객체) > 영속컨텍스트(영속객체) > DB(레코드)
>> 커밋 시점에 DB에 반영된다.

* Entity Mapping
> 1. @Entity : 엔티티 클래스에 설정, 필수
> 2. @Table : 매핑 테이블 지정 (생략하면 클래스 이름과 동일한 이름에 매핑)
>> - name : 테이블 이름 (생략하면 클래스 이름과 동일한 이름)
>> - catalog : 카탈로그 이름 (DB 이름)
>> - schema : 스키마 이름
> 3. @Id : 식별자 속성게 설정, 필수
> 4. @Column : 매핑할 칼럼명 지정
> 5. @Enumerated : enum 타입 매핑할 때 설정
>> - EnumType.STRING : enum 타입 값 이름을 저장 (문자열 타입 컬럼에 매핑)
>> - EnumType.ORDINAL : enum 타입 값 순서를 저장 (숫자 타입 컬럼에 매핑) (거의 사용안함, 추가되면 순서 )
```java
public enum Grade{
 S1,S2,S3...
}
```
```java
EnumType.STRING -> "S1"
Grade.S1.name()
```
```java
EnumType.ORDINAL -> 0
Grade.S1.ordinal()
```
* Entity 클래스 제약조건
> 1. @Entity 적용해야함
> 2. @Id 적용해야함
> 3. 인자 없는 기본 생성자 필요
> 4. 기본 생성자는 public이나 protected 여야 함
> 5. 최상위 클래스여야 함
> 6. final이면 안됨

* 접근 타입
> 1. 2개의 접근 타입
>> 1. Field 접근 : 필드 값을 사용해서 매핑
>> 2. Property 접근 : getter/setter 메서드를 사용해서 매핑
>2. 설정방법
>> 1. @Id 어노테이션을 필드에 붙이면 필드 접근
>> 2. @Id 어노테이션을 getter에 붙이면 프로퍼티 접근
>> 3. @Access 어노테이션을 사용해서 명시적으로 지정
>>> 클래스/개별 필드에 적용 가능 <br>
>>> @Access(AccessType.PROPERTY) / @Access(AccessType.FIELD) <br>
>3. Field 접근 선호 : 불필요한 setter메서드를 만들 필요 없음.
