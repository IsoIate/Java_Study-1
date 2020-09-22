# 백기선의 스프링 부트
 - 강좌를 3개로 나눕니다  
```xml
   - 스프링 부트 원리
   - 스프링 부트 활용
   - 스프링 부트 운영
```  
 ## 1.스프링 부트의 원리  
   #### 스프링 부트란?  
   - 제품수준의 스프링 기반의 독립적인 어플리케이션을 만들 때 빠르고 쉽게 만들 수 있는 장점이 있다.  
   - 기본적인 설정을 제공, 쉽고 명확하고 커스터마이징하기 쉽다.  
   - codegeneration 및 xml configuration을 사용하지 않는게 목표  
   #### 스프링 부트 프로젝트 구조  
   - Maven프로젝트와 동일
   ```xml
    소스코드 (src/main/java)
    소스 리소스 (src/main/resources)
    테스트 코드 (src/test/java)
    테스트 리소스 (src/test/resources)
   ```
   #### 스프링 부트의 의존성 관리
   - Maven의 계층구조로 우리가 직접 버전을 작성을 안해도 됨
   - parent속성으로 spring-boot-starter-parent... spring-boot-dependencys.... 등등 부모 오브젝트에서 스타터에 있는 의존성들 추가
   - 따로 사용하고싶은 버전이나 외부 라이브러리를 사용하고싶다면 직접 명시해주면 됨
   #### 자동 설정  
   ##### EnableAutoConfiguration
   - SpringBoot가 bean 등록하는 단계는 총 두 단계가 있다.
     1. ComponentScan으로 한번 스캔한 후
     2. EnableAutoConfiguration으로 다시한번 스캔
   - ComponentScan
     - Component라는 어노테이션을 가진 클래스들을 스캔해서 빈으로 등록
     - Component로 포함되는 멤버들
       1. Configuration
       2. Repository
       3. Service
       4. Controller
       5. RestController
   - EnableAutoConfigration
     - org.springframework.boot.autoconfigure.EnableAutoConfiguration의 META-INF의 spring.factories에서 Spring Meta file을 읽어 들어오는 것, 결국 Configuration으로 빈으로 등록이 됌
   #### 내장 서블릿 컨테이너
   - ServletWebServerFactoryAutoConfiguration (서블릿 웹 서버 생성)
   - TomcatServletWebServerFactoryCustomizer (서버 커스터마이징)
   #### 내장 서버 Customizing 
   - application.properties
   ```xml
    spring.main.web-application-type=none -->> 웹서버 X
    server.port=0 -->> 랜덤 포트
   ```
   #### 내장 웹 서버 응용, HTTPS와 HTTP2
   - HTTP
   - HTTP/2 - ?? 사용하려면 SSL을 반드시 해야한다라고하심
    - http2 enable을 해줘야함
   ```xml
     ...../applcation.properties
     server.http2.enable=true 
   ```
   - HTTPS
   #### 독립적으로 실행 가능한 JAR
   - mvn package를 하면 실행 가능한 JAR파일 "하나가" 생성 됨
   - spring-maven-plugin이 해주는 일(패키징)
   - 과거 "uber" jar를 사용
     - 모든 클래스(의존성 및 애플리케이션)를 하나로 압축하는 방법
     - 뭐가 어디서 온건지 알 수가 없었음
   - 스프링 부트의 전략
     - 내장 JAR : 기본적으로 자바에는 내장  JAR를 로딩하는 표준적인 방법이 없ㅇ므.
     - 애플리케이션 클래스와 라이브러리 위치 구분
       - org.springframework.boot.loader.jav.JarFile을 사용해서 내장 JAR를 읽음
       - org.springframework.boot.loader.Launcher를 사용하여 실행한다.
   
