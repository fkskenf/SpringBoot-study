# Feign Client Log 찍는 방법
> 1. application.yml 추가
```yml
logging:
  level:
    "[com.wehago.finance.client.objectstorage.ObjectStorageClientDelegate]": DEBUG
    io:
      lettuce:
      ...
```
> 2. FeignConfiguration 추가
```java
@Bean
Logger.Level feignLoggerLevel() {
  return Logger.Level.FULL;
}
```
