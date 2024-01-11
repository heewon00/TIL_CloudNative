## 📝 WAR 빌드&배포와 톰캣 설정
### **🐱** 프로젝트 빌드

- 프로젝트 빌드 : `./gradlew build`
- WAR 파일 생성 확인 : `build/libs/server-0.0.1-SNAPSHOT.war`
- 압축 풀기 후 WEB-INF 파일 확인하기 : `jar -xvf server-0.0.1-SNAPSHOT.war`

### **🐱 JAR, WAR 간단 소개**

- JAR 소개
    - 자바는 **여러 클래스와 리소스를 묶어서 `JAR` (Java Archive)라고 하는 압축 파일을** 만들 수 있다. 이 파일은 JVM 위에서 직접 실행되거나 또는 다른 곳에서 사용하는 라이브러리로 제공된다.
    - 직접 실행하는 경우 `main()` 메서드가 필요하고, `MANIFEST.MF` 파일에 실행할 메인 메서드가 있는 클래스를 지정해 두어야 한다.
        - 실행 예) `java -jar abc.jar`
    - Jar는 쉽게 이야기해서 클래스와 관련 리소스를 압축한 단순한 파일이다. 필요한 경우 이 파일을 직접 실행할 수도 있고, 다른 곳에서 라이브러리로 사용할 수도 있다.
- WAR 소개
    - WAR(Web Application Archive)라는 이름에서 알 수 있듯 WAR 파일은 웹 애플리케이션 서버(WAS)에 배포할 때 사용하는 파일이다.
    - **JAR 파일이 JVM 위에서 실행된다면, WAR는 웹 애플리케이션 서버 위에서 실행된다.**
    - 웹 애플리케이션 서버 위에서 실행되고, HTML 같은 정적 리소스와 클래스 파일을 모두 함께 포함하기 때문에 JAR와 비교해서 구조가 더 복잡하다. 그리고 WAR 구조를 지켜야 한다.
- WAR 구조
    - WEB-INF
        - `classes` : 실행 클래스 모음
        - `lib` : 라이브러리 모음
        - `web.xml` : 웹 서버 배치 설정 파일(생략 가능)
    - `index.html` : 정적 리소스
    - `WEB-INF` 폴더 하위는 **자바 클래스와 라이브러리, 그리고 설정 정보**가 들어가는 곳이다.
    - `WEB-INF` 를 제외한 나머지 영역은 **HTML, CSS 같은 정적 리소스**가 사용되는 영역이다.

### **🐱 WAR 배포**

1. 톰캣 서버를 설치한 폴더의 bin 폴더로 이동 후 톰캣 서버를 종료한다. `./shutdown.sh`

2. `톰캣폴더/webapps` 하위를 모두 삭제한다.

3. 빌드된 `server-0.0.1-SNAPSHOT.war` 를 복사한다.

4. `톰캣폴더/webapps` 하위에 붙여넣는다.

- `톰캣폴더/webapps/server-0.0.1-SNAPSHOT.war`

5. 이름을 변경한다. `톰캣폴더/webapps/ROOT.war`

6. 톰캣 서버를 실행한다. `./startup.sh`

- `톰캣폴더/bin` 폴더
    - 실행: `./startup.sh`
    - 종료: `./shutdown.sh`
- 실행을 하면 ROOT.war파일을 알아서 압축 해제함
- 결과 확인
    - http://localhost:8080/index.html
    - http://localhost:8080/test
    - `톰캣폴더/logs/catalina.out` 로그
        
- 인텔리J나 이클립스 같은 IDE는 이 부분을 편리하게 자동화해준다.

### **🐱 톰캣 설정 (유료)**

- 종료: `./shutdown.sh`
- 상단Run → Run…
    - Server: Application server을 Tomcat으로 설정
    - Deployment: Gradle: ~~~.war(exploded) 설정 후 Application context 삭제하기
    
- 오류
    - Execution failed for task ':compileJava'.
    invalid source release: 17
    - 해결 : [https://velog.io/@persestitan/Spring-Cause-error-invalid-source-release-17-해결방법-IntelliJ](https://velog.io/@persestitan/Spring-Cause-error-invalid-source-release-17-%ED%95%B4%EA%B2%B0%EB%B0%A9%EB%B2%95-IntelliJ)
    

### **🐱 톰캣 설정 (무료)**

- War 파일을 풀어서 특정 디렉토리에 저장
    
    ```jsx
    //war 풀기, 인텔리J 무료버전 필요
    task explodedWar(type: Copy) {
        into "$buildDir/exploded"
        with war
    }
    ```
    
    `baki@baghuiwons-MacBook-Pro build % cd ..`
    `baki@baghuiwons-MacBook-Pro server % ./gradlew explodedWar`
    
    build/exploded 폴더가 새로 생성되고, 여기에 WEB-INF 를 포함한 war가 풀려 있는 모습을 확인할 수 있다.
    

- Preferences에서 Smart Tomcat 설치하기
- 메뉴 -> Run -> Edit Configurations
<img width="852" alt="image" src="https://github.com/heewon00/TIL_CloudNative/assets/55778040/ac433479-ec2d-46e9-86fb-194d452cb8d7">