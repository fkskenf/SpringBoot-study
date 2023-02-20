Study : https://wildeveloperetrain.tistory.com/32

# 설명
Spring Boot에서는 Spring Data Redis를 통해 <br>
Lettuce, Jedis 라는 두가지 오픈소스 Java 라이브러리를 사용할 수 있습니다. <br>
이번시간에는 Lettuce 사용하여, 적용해 보겠습니다.

- Spring Boot에서 Redis를 사용하는 방법
> 1. RedisTemplate (이번시간에는 이것을 활용)
> 2. RedisRepository

# 적용
1. 의존성 추가 (dependency)
```java
implementation 'org.springframework.boot:spring-boot-starter-data-redis'
```

2. application.yml (접속정보 추가)
```yml
session-redis:
  host: 172.16.***.***
  port: 6379
  password: password!
```

3. RedisProperties 생성 
> - RedisProperties를 사용할 수 도 있으나, 생성해서 사용하기도 하는것 같다. (아래 로직 처럼 생성해서 사용) 
> - RedisProperties를 통해서 properties(application.yml)에 저장한 host, port를 가지고 와서 연결합니다.
> - @ConfigurationProperties는 properties 파일에 정의된 프로퍼티 중 주어진 prefix 를 가지는 프로퍼티들을 POJO 에 매핑하여 Bean 으로 만들수 있게 해주는 어노테이션이다.

```java
import lombok.Getter;
import lombok.Setter;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties("session-redis")
@Getter
@Setter
public class SessionRedisProperties {

	private String host;
	private int port;
	private String password;

}
```

4. Redis 저장소와 연결 (Config 파일 생성)
> - setKeySerializer, setValueSerializer 설정해주는 이유 <br>
> : RedisTemplate를 사용할 때 Spring - Redis 간 데이터 직렬화, 역직렬화 시 사용하는 방식이 Jdk 직렬화 방식이기 때문입니다. <br>
동작에는 문제가 없지만 redis-cli을 통해 직접 데이터를 보려고 할 때 알아볼 수 없는 형태로 출력되기 때문에 적용한 설정입니다.
```java
import com.common.property.SessionRedisProperties;
import lombok.RequiredArgsConstructor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.connection.RedisStandaloneConfiguration;
import org.springframework.data.redis.connection.lettuce.LettuceConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.StringRedisSerializer;

@Configuration
@RequiredArgsConstructor
public class SessionRedisConfig {

	private final SessionRedisProperties sessionRedisProperties;

  // lettuce : RedisConnectionFactory 인터페이스를 통해 LettuceConnectionFactory를 생성하여 반환합니다
	@Bean
	public RedisConnectionFactory sessionRedisConnectionFactory() {
		RedisStandaloneConfiguration redisStandaloneConfiguration = new RedisStandaloneConfiguration();
		redisStandaloneConfiguration.setHostName(sessionRedisProperties.getHost());
		redisStandaloneConfiguration.setPort(sessionRedisProperties.getPort());
		redisStandaloneConfiguration.setPassword(sessionRedisProperties.getPassword());
		return new LettuceConnectionFactory(redisStandaloneConfiguration);
	}

	@Bean
	public RedisTemplate<String, String> sessionRedisTemplate() {
		RedisTemplate<String, String> redisTemplate = new RedisTemplate<>();
		redisTemplate.setConnectionFactory(sessionRedisConnectionFactory());
		redisTemplate.setKeySerializer(new StringRedisSerializer());
		redisTemplate.setValueSerializer(new StringRedisSerializer());
		return redisTemplate;
	}

}

```

5. 사용
```java
import org.springframework.data.redis.core.RedisTemplate;

private final RedisTemplate<String, String> sessionRedisTemplate;

... 

// Session Redis 에는 token 을 key 로, user 정보가 stringify 형태인 값이 value 로 저장되어 있음
String user = sessionRedisTemplate.opsForValue().get(token);
if (user == null) {
  handleUnauthorized(response, "유저 정보가 없습니다.");
  return;
}
```
