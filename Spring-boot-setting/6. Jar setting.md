1. build.gradle 추가
```gradle
bootJar {
	archiveFileName = 'demo.jar'
}
```
2. Terminal 입력
```terminal
./gradlew bootjar
```
3. 프로젝트 > build > libs> jar파일 생성

![image](https://user-images.githubusercontent.com/60438691/212850370-a9ace939-af63-472f-ab95-65d4a83c7dae.png)
