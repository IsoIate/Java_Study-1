# 백기선의 스프링 부트

## 스프링 부트 활용
### SpringApplication
 - 아무것도 안하고 run을 할 때, 기본 로그 레벨은 INFO
 -  FailureAnalyzer
  - 에러가 났을 때 에러 메세지를 이쁘게 해줌..
 - 배너 src/main/resource/ 에 파일을 만들어서 넣으면 됨, banner.txt | gif | jpg | png
 - 위치를 바꾸고싶다면 applcation.banner.location=classpath:... 로 변경 가능
 - 스프링 부트는 특이한게 패키징할 때 jar파일 하나가 생기는데, 그 안에 모든 의존성이나 데이터들이 들어가서 독립적으로 실행가능함.
###### ApplicationEvent 등록
 - 스프링에서 기본적으로 지원해주는 Event가 있음
 - 이벤트들은 다양한 시점이 있다.
  - 어플리케이션이 시작됐을 때
  - 어플리케이션이 준비가 다 됐을 때 등등..
 - ApplicationStartingEvent -> ApplicationContext 생성 전에 실행
 - ApplicationStaringEvent는 ApplicaitonContext 생성 전에 실행하는 이벤트이므로 빈으로 등록해도 실행이 안돼서 직접 Event를 등록해줘야함
 ```java
 SpringApplication application = new SpringApplication(SpringinitApplication.class);
	application.addListeners(new SampleListener());
	application.run(args);
 ```  
 - WwebApplicationType 설정 -> 기본이 Servlet
 - AplicationArgument
  - VM Option --> -D...
  - Program Argument --> --...
 - 어플리케이션을 실행 한 뒤 뭔가 싫행하고 싶을 때
  - ApplicationRunner(추천) 또는 CommandLineRunner
  - 순서 지정 가능 @Order
  ###### 외부설정
   - 사용할 수 있는 외부 설정
     1. properties
     2. YAML
     3. 환경 변수
     4. 커맨드 라인 아규먼트
