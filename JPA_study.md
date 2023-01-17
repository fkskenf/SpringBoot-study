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

