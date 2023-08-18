# sprintboot-upgrade
개발라이브러리 및 빌드팩 업그레이드


## Redis 6.0.6 확인
 ```
redis  삭제 방법 :https://learn2torials.com/a/how-to-uninstall-redis-server-from-ubuntu
redis 6.0.6 설치(ubuntu18.04 LTS): https://otodiginet.com/database/how-to-install-and-configure-redis-6-0-on-ubuntu-20-04-lts/
javabuildpack 정보 : https://github.com/cloudfoundry/java-buildpack/releases
 ```

## 0.터미널에서 Gradle 버전 업그레이드
```
$cd ~/Downloads
$wget https://services.gradle.org/distributions/gradle-6.7-all.zip
$unzip gradle-6.7-all.zip
$mv ~/dev/gradle ~/dev/gradle-backup
$mv gradle-6.7 ~/dev/gradle
$gradle --version
```
- .bashrc 변경

```
vi ~/.bashrc
./gradlew -> gradle 변경

$ source ~/.bashrc
```

## 1. lombok 버전 업그레이드
https://github.com/freefair/gradle-plugins
https://sehajyang.github.io/2019/11/12/springboot-migration-1.5to2.0/
 ```
distributionUrl=https\://services.gradle.org/distributions/gradle-6.7-all.zip
build.gradle :     id 'io.freefair.lombok' version '5.3.3.3'
```

## 2.SpringFramework 버전 upgarde
Release note 확인 :https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.3-Release-Notes
```
build.gradle에서 git diff를 이용하여 추가된 내용을 확인
spring boot framework  필수 사항
 - 최신 RELEASE 사용 
-  관련 개발용 library 에 대한 compatability  matrix check 필수

참고 사이트 :
https://jsonobject.tistory.com/529
https://yonguri.tistory.com/123
https://sehajyang.github.io/2019/11/12/springboot-migration-1.5to2.0/
```

## 3.SpringBoot 변경에 따른 기타 라이브러리 버전 및 소스 변경  
https://spring.io/projects/spring-cloud 
https://spring.io/projects/spring-cloud-stream

- Spring Cloud Stream ( Greenwich --> Hoxton )  , root folder(build.gradle) modify
```
set('springCloudVersion', "Greenwich.SR3")  ->     set('springCloudVersion', "Hoxton.SR11")
```
- Mongodb 관련 소스 변경
MongoConfiguration.java 파일의 없어진 함수 수정, new Sort -> Sort.by 변경
```
mongdgo version 변경 2.x to 3.x  https://docs.spring.io/spring-data/mongodb/docs/current-SNAPSHOT/reference/html/#upgrading.2-3
```
- Active Profile Cloud 추가
```
application-cloud.yml 추가 (vcap service에 credential 정보 가져오기)
 MongoConfiguration.java 수정
```
- gradle&node 플러그인 변경
component:bff or bffonm의 build.gradle, 즉 nodejs가 포함된 컴포넌트임
https://github.com/node-gradle/gradle-node-plugin
```
//기존 삭제   id "com.moowork.node" version "1.3.1"  
 id "com.github.node-gradle.node" version "3.1.0"
```

- Multiple Repository 사용시 고려사항 : https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.multiple-modules
- App에서 Service Instance에 connection 이 제대로 안 되는 경우 unbind 서비스 후에 cf push를 수행

## java openJdk 16 추후 적용 시 참고
spring framwork 2.3.11.RELEASE  : java openjdk15 까지만 지원 가능
```
Update the package index:
# sudo apt-get update
Install openjdk-16-jdk deb package:
# sudo apt-get install openjdk-16-jdk 
```
