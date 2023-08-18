### 1.OpenJDK 17 업그레이드
- 설치 방법
```
sudo apt update
sudo apt install openjdk-17-jdk
sudo apt install openjdk-17-jre
sudo update-alternatives --config java
sudo update-alternatives --config java <- 번호 선택 시 17버전 Selection 번호 입력
java -version <- 변경된 버전 확인
```
- 변경 사항 : https://luvstudy.tistory.com/151
- https://github.com/johanjanssen/javaupgrades

###  2.IntelliJ sdk 설정 변경
File > Project Structure > Project Settings > Project > SDK/Language level 설정 17로 변경
File > Project Structure > Platform Settings > SDKs > 17 선택 변경

Setting>Build, Execution, Deployment > Gradle > Gradle JVM : 11 => 17 로 변경

### 3.gradlew 버전 업그레이드
버전 확인 : https://services.gradle.org/distributions/
```
$./gradlew wrapper --gradle-version 7.4.2
$./gradlew -v
```

### 4.spring boot/cloud support 
호환 버전 확인 : https://docs.spring.io/spring-cloud/docs/current/reference/html/
Release Train Version: 2021.0.3 / Supported Boot Version: 2.6.8

build.gradle spring cloud/boot 버전 변경
```
ext {
    set('springCloudVersion', "2021.0.3")
}
id 'org.springframework.boot' version '2.6.8'
```

MongoTest 관련 수정
```java
@DataMongoTest
public class PostRepositoryTest {}
```

test/resources/application.yml 추가 -> SpringBoot 2.6 이상 버전부터는 spring.mongodb.embedded.version을 설정해야한다
```
spring:
  mongodb:
    embedded:
      version: 3.5.5
```
Jpa/mongo repository 관련 spy 클래스 Override 함수 추가

Spring Security Cors Mapping Error
```
에러 메세지만 잘 읽어도 무엇이 문제인지 바로 알 수 있는데 spring security에서 cors설정을 할때 .addAllOrigin("*") 과 .allowCrededentials(true)를 동시에 사용할 수 없어서 나는 에러이다.
allowCredential이 true라면 “Access-Control-Allow-Origin” 응답 헤더헤더를 설정 할 수 없기 때문에 특수 값인 "*"를 사용 못하는 것이다.
해결방법은 특정 주소만 허락하거나, addAllOrigin("*") 대신 .addAllOriginPatterns("*")를 사용하면 된다.
```

AWS SDK 버전 업그레이드(build.gradle)
```
    implementation 'com.amazonaws:aws-java-sdk-s3:1.12.243'
    implementation 'com.amazonaws:aws-java-sdk-ses:1.12.243'
```

- https://spring.io/projects/spring-boot#suppor

spring-cloud-stream 관련 - Deprecated 함수 수정
-  조치방법 : https://www.pivotaltracker.com/story/show/177960819/comments/232229500
- 참고 :http://shaikezam.com/spring_cloud_stream_functional
- Annotation vs Function 비교 : https://medium.com/@odysseymoon/spring-cloud-stream-with-rabbitmq-c273ed9a79b


jenkins설정
- openjdk 설정 파이프라인 파일의 추가(jenkins 서버 설정 방법 : https://blog.leocat.kr/notes/2020/02/01/jenkins-multiple-jdk-on-jenkins)
```
pipeline {
  tools {
    jdk("OpenJdk17")
  }
}
```

manifest 파일 jdk 버전 수정
```
    env:
      JBP_CONFIG_OPEN_JDK_JRE: '{ jre: { version: 17.+ } }'
```


### 5. Makefile 수정
gradle    사용시 version 6.7을 가지고 옴. 
gradlew 사용시 version7.4.2를 가지고 오므로, 명령어를 수정함
```
    ./gradelw ~~~~
```

### 기존 업그레이드 참고 : #178387471

