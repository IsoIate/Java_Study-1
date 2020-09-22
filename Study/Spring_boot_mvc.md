# Spring Web MVC 

## 서블릿(Servlet)
- 자바 엔터프라이즈 에디션에서 제공하는 자바로 웹 어플리케이션을 개발할 수 있는 스펙과 API를 제공
- 가장 핵심적인 클래스가 HttpServlet, 서블릿의 특징은 한 요청을 처리할 때마다 프로세스가아닌 쓰레드를만들어서 사용, 이전에 사용하던 Common Gateway Interface와 같은 기술에 비해서 훨씬 빠르고, Java기반이라서 OS의 독립적이고 Java가 가지고있는 보안기능을 가지고있기 때문에 보안 및 이식성이 좋다.

## 서블릿 엔진 또는 서블릿 컨테이너 (톰캣, 제티, 언더토 ..)
- 세션관리
- 네트워크 서비스
- MIME 기반 메시지 인코딩 디코딩
- 서블릿 생명주기 관리 등

## 서블릿 생명주기
1. init()
2. service() -> doGet(), doPost()
3. destory()

## 서블릿 리스너
 - 웹 어플리케이션에서 발생하는 주요 이벤트를 감지하고 각 이벤트에 특별한 작업이 필요한 경우에 사용 가능
 - 서블릿 컨텍스트 리스너의 종류 
   1. 서블릿 컨텍스트 수준의 이벤트  
     a. 컨텍스트 라이프사이클 이벤트  
     b 컨텍스트 애트리뷰트 변경 이벤트  
   2. 세션 수준의 이벤트  
     a. 세션 라이프사이클 이벤트  
     b. 세션 애트리뷰트 변경 이벤트  
   
## 서블릿 필터
- 들어온 요청을 서블릿으로 보내고, 또 서블릿이 작성한 응답을 클라이언트로 보내기 전에 특별한 처리가 필요한 경우에 사용할 수 있다.
- 체인 형태의 구조라서 동시다발적으로 이루어지는게아니라 순서대로 실행됨

## 서블릿 어플리케이션에 스프링 연동하기
- 서블릿에서 스프링이 제공하는 IoC 컨테이너 활용하는 방법
  - ContextLoaderListener는 스프링 IoC 컨테이너, 스프링 어플리케이션 컨텍스트를 서블릿 어플리케이션 컨텍스트 생명주기에 맞춰서 바인딩 해주는 것
- 스프링이 제공하는 서블릿 구현 DispatcherServlet 사용하기

## DispatcherServlet
- FrontController 패턴으로 구현되어있음
- WebApplicationContext를 상속받아 새롭게 WebApplicationContext를 만든다. ContextLoaderListener가 만들어준 ApplicationContext는 다른 서블릿도 공유해서 사용할 수 있음. scope 자체가 다르다. 그리고 DispatcherServlet안에서 만든 ApplicationContext는 DispatcherServlet안에서만 scope가 한정되어있기 때문에 혹시라도 다른 DispatcherServlet에서는 ApplicationContext를 알 수 없음.
- RootWebApplicationContext는 Web과 관련된 빈들은 등록이 되지 않음, Service와 Repository같이 다른 DispatcherServlet도 공용으로 쓸 수 있으니 필요가 없음
- DispatcherServlet이 만드는 ApplicationContext는 Controller, ViewResolver, HandlerMapping 같이 Web과 관련된 빈들만 등록, 빈을 등록하는 해당 DispatcherServlet에 한정적임
