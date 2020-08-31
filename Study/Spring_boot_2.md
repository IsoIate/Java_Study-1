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
 - ApplcationContext 
