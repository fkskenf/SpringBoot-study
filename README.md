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
