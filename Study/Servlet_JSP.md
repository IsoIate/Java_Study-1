# Servlet/JSP 강의 01 ~ 32
자바 -> 자바웹 프로그래밍  
자바 웹프로그래밍
1. 서블릿 (HTML 코드 출력하기가 너무 힘듬)
2. JSP (소스코드가 많아지면 스파게티 코드가 발생)
3. JSP MVC 
4. Spring MVC

순서로 공부? 발전  

기존의 네트워크 프로그램은 클라이언트와 서버가 업데이트 될 때마다 소켓을 사용하여 연결을 해서 프로그램을 삭제 후 재설치하는 방법을 사용했었는데 HTTP(웹)을 사용해 웹으로 클라이언트에게 서버가 업데이트된 데이터를 전송할 수 있다.  

웹 어플리케이션 서버 WAS  
프로그램을 동적으로 실행할 수 있는 WAS 서버가 필요  
코드, Server App을 실행할 수 있는 환경이 WAS이다.  
 
 
실행환경
Web Server + WAS + Server App  


Servlet -> __Serv__ er Application __Let__  서버의 조각?  

아파치 톰캣 포트 변경 -> 톰캣경로/conf/server.xml 에서 Connector 포트 변경  
실행 방법 -> 톰캣경로/bin/startup.bat 실행!  

연결 확인 법 -> localhost:본인이 정한 포트 ex)8080  


톰캣은 기본적으로 아무것도 입력하지 않았을 때 index.html을 가르킨다.

### Context 사이트 추가
http//IP(or hostName):Port/context/리소스들
Server.xml -> Host 안에 추가
```xml
<Context path="it" docBase = "C:\tools\apache-tomcat-9.0.36\webapps\ITWeb\it" privileged="true">
```


HttpServlet을 상속받은 객체 service 메서드를 오버라이딩해서 사용 
 -> post, get 둘다 응답 가능  (HttpServletRequest, HttpServletResponse 두 객체를 매개변수로 받음)  
 doGet이나 doPost 메서드 사용 가능

톰캣 경로의 ROOT안에 있는 WEB-INF 폴더는 절대 사용자가 요청할 수 없고 서버를 위해 사용  
그래서 ~~그 곳에 클래스 파일을 둠~~ (대충 중요한파일 놓는다는거같음)  

### 서블릿이 실행되는 시점과 방식
요청에따라 WAS 서버가 자바 서버 어플리케이션 안에있는 여러가지 코드를 분류해서 실행시킴  

서블릿 매핑하는 방법은 WEB-INF에 web.xml에 다음과 같은 코드를 추가해야함  

```xml
<servlet>
    <servlet-name>na</servlet-name>
    <servlet-class>Nana</servlet-class>
  </servlet>

  <servlet-mapping>
    <servlet-name>na</servlet-name>
    <url-pattern>/hello</url-pattern>
  </servlet-mapping>
```
### 추후 Java의 Stream과 Writer Reader의 차이점 공부할것 ~~(아마 버퍼차이같은데 자세한게 기억이안난다)~~    
Writer, Reader -> I/O를 문자 단위로 Char 단위로 읽음
stream -> I/O를 byte단위로 읽음(한글깨짐- 한글 2byte, stream 1byte)  


이클립스에서의 첫 웹  
[JSPPrj](https://github.com/eggme/JSPPrj)


### web.xml 말고 Annotation을 이용한 URL 매핑  
클래스 위에 @WebServlet("URL 매핑") 어노테이션으로 매핑 가능  
어노테이션이란 클래스나 메소드위에 붙어있는 주석?  
컴파일러해도 지워지않는 주석?  
클래스의 메타 데이터

원래 web.xml에서 위의 매핑을 해야하는데 어노테이션으로 간단하게 가능  

공동작업을할때 불편함이 있어서 서블릿매핑으로 처리하는게 깔끔합니다

크롬은 text로 해석하고 엣지나 익스플로러는 html로 해석하기때문에 문서에 컨텐츠 형식을 지정해줘여한다.  (헤더의 중요성을 말하는거 같다)
브라우저에 컨텐츠 형식을 알려주지 않은 경우 -> 자의적인 해석을 함

웹 서버에서 클라이언트에게 보내질 때 웹서버에서 보내는 데이터의 단위가 ISO-8895-1 형식이다.  
그럴 때 한글 깨짐 현상이 발생하기 때문에 보내는 데이터에 따라 인코딩을 바꿔야함  

위의 내용들을 모두 해결하려면 Servlet에 다음과 같은 코드를 입력하면 된다.   
```java
(HttpServletResponse obj)response.setChatacterEncoding("UTF-8");
(HttpServletResponse obj)response.setContentType("text/html;charset=UTF-8");
```   

### Client의 요청
- GET 방식
 - QueryString을 사용하여 주소에 계속 더해짐  
 - 추가적인 옵션 설정
 - 보안 문제 발생 가능성 (아이디, 패스워드 등)
``` 
http://localhost/hello  
http://localhost/hello?cnt=3	-> 3
http://localhost/hello?cnt=	-> ""
http://localhost/hello?		-> null
http://localhost/hello		-> null
```   
넘어오는 get방식의 파라미터를 가져오려면 request객체의 getParameter메서드를 사용하여 가져옴   
```
(HttpServletRequest obj)request.getParameter("파라미터 이름");
```
- POST 방식
 - 넘어오는 요청이 많을 때 두 단계로 나누어서 일을 처리
 - GET방식과 다르게 주소창에 더해지지 않음
 - 주소에 더해지는 방식이 아니므로 글자 수 제한이 없음

 페이지 인코딩 바꾸는법
 
 이클립스 Window -> Preferences -> 밑에 Web -> html, css, jsp Encoding 변경하면 파일 생성시 자동으로 인코딩 변경됨.  
 
 request Paremeter은 form 태그 input 요소에 name 값을 통해 값을 전달하기 때문에 보내는 데이터에 반드시 name을 써줘야한다.  
 
 #### 한글 입력 문제  
 Client(UTF-8 : 2byte) -> Server(ISO-8859-1 : 1byte)  
 한글깨짐 현상 발생  
 해결법  
 1. request CharacterEncoding 변경  
 2. tomcat servet.xml에 Encoding 추가   
 일반적으로 톰캣서버의 설정은 건드리지 않는게 좋음 (여러 서버가 있을 수 있으니)   
 
 #### Servlet Filter
 서블릿 접근 전 또는 후에 적용되는 조건?
 필터 적용법  
 1. web.xml에 맵핑
 ```xml
 <filter>
  	<filter-name>필터이름</filter-name>
  	<filter-class>패키지명.클래스명</filter-class>
  </filter>
  <filter-mapping>
  	<filter-name>필터이름</filter-name>
  	<url-pattern>/*</url-pattern> -> 모든 url 지정
  </filter-mapping>
 ```
 
 필터클래스
 ```java
 public class CharacterEncodingFilter implements Filter {

	@Override
	public void doFilter(ServletRequest request
			, ServletResponse response
			, FilterChain chain)
			throws IOException, ServletException {
		// TODO Auto-generated method stub
		// 다음 페이지로 넘기는 권한은 FilterChain
		System.out.println("Hello Filter");
		request.setCharacterEncoding("UTF-8");
		chain.doFilter(request, response);
		// 흐름을 넘기는 것 해당 request와 response에 담긴 요청 실행
		// 말그대로 한번 들렸다가 거르는 것
	}

}
 ```
 2. 어노테이션 사용
 ```java
 @WebFilter("/*")
  public class CharacterEncodingFilter implements Filter {

	@Override
	public void doFilter(ServletRequest request
			, ServletResponse response
			, FilterChain chain)
			throws IOException, ServletException {
		// TODO Auto-generated method stub
		// 다음 페이지로 넘기는 권한은 FilterChain
		System.out.println("Hello Filter");
		request.setCharacterEncoding("UTF-8");
		chain.doFilter(request, response);
		// 흐름을 넘기는 것 해당 request와 response에 담긴 요청 실행
		// 말그대로 한번 들렸다가 거르는 것
	}

}
 ```
 
 21강 학습과제 (add.html)  
 [JSPPrj 학습과제 링크](https://github.com/eggme/JSPPrj/blob/master/src/com/lsj/web/Calcurator.java)  
 
 
 request객체에서 여러개의 값을 가져오는 법
 ```html
<input type="text" name="value" /> 
<input type="text" name="value" />
<input type="text" name="value" />
<input type="text" name="value" /> ....
```

일때 value들을 한번에 배열 형태로 받는 법
```java
String[] values = request.getParameterValues("value");
```

웹의 상태 유지를 위한 5가지 방법  
1. application  
 - Application 저장소 : 서블릿 컨텍스트 (Servlet Context)  
 ```java
 ServletContext application = req.getServletContext();
 // // ServletContext -> 컬렉션이라고 생각
 application.getAttribute("키");
 application.setAttribute("키", "값");
 
 // 페이지 이동
 response.sendRedirect("경로");
 ```
2. session  
- session은 __현재 접속자__ 에 대한 데이터, application은 어플리케이션 전체에서 돌아가는 개념
```java
 HttpSession session = req.getSession();
 session.getAttribute("키");
 session.setAttribute("키", "값");
 ```  
- 브라우저 별로 다른 세션으로 구분 (크롬, 파이어폭스, 익스플로러...)  
- 같은 브라우저는 같은 세션으로 인식 (크롬 창을 2개 띄웠을 때)
- WAS에 어플리케이션 공간에 사용자 마다 저장 할 수 있는 세션 공간이 있음  
- 사용자를 식별하는 아이디를 SID(Session ID)를 사용하여 세션 공간에 데이터를 저장 (처음에는 SID가 없음)  
- 처음 실행 시 어플리케이션 공간만 가능, 로그인을 하였을 때 SID를 부여하여 세션을 통하여 데이터를 보관  
- 브라우저 종료 시 SID 삭제
- 쿠키로 세션 아이디를 전송  
```java
// 세션관련 메서드
void setAttribute(String name, Object value)
// 지정된 이름으로 객체를 설정
Object getAttribute(String name)
// 지정한 이름의 객체를 반환
void invalidate()
// 세션에 사용되는 객체들을 바로 해제
void setMaxInativeInterval(int interval)
// 세션 타임아웃을 정수(초)로 설정
boolean isNew()
// 세션이 새로 생성되었는지를 확인 
Long getCreationTime() // 기본 타임아웃은 30분
// 세션이 시작된 시간을 반환, 1970년 1월 1일을 시작으로 하는 밀리초
Long getLastAccessedTime()
// 마지막 요청 시간, 1970년 1월 1일을 시작을 하는 밀리초
```
3. cookie  
- 상태 저장을 위한 값
- 클라이언트에 저장하는 저장소  
- Header 정보와 사용자 데이터를 가지고 있음  
- 데이터의 유형을 이해해서 용도별로 맞는 처리를 해야함 Application, Session, Cookie...  
```java
// 쿠키 저장하기
Cookie cookie = new Cookie("c", String.valueOf(result));
response.addCookie(cookie);
// 쿠키 읽기
Cookie[] cookies = request.getCookies();
String _c = "";
if(cookies != null){
    for(Cookie cookie : cookies){
    	if("c".equals(cookie.getName())
	    _c = cookie.getValue();
    }
}
```
- cookie를 사용할 때 알아야하는 옵션 -> 서블릿마다 쿠키가 다르니까, URL설정을 한다. 그래야만 쿠키를 효율적으로 사용할 수 있다.  
- setPath() 메소드를 사용하여 쿠키 path설정, 원하는 url에서만 사용 가능하도록 설정 가능  
- cookie의 maxAge 설정 중요) 쿠키의 가장 큰 장점 -> 브라우저가 닫혀도 내가 원하는 기간을 설정하게 된다면 그 기간내에는 값을 유지 할 수 있음.  
- 쿠키는 기본적으로 브라우저 메모리에있다가, 외부파일으로 저장됨.  
```java
cookie.setPath("/"); 
// 모든 요청에 쿠키설정
cookie.setPath("/notice");
// notice 요청에게만 쿠키설정
cookie.setMaxAge(1000);
// miliescound 후 종료 ) 1000초 후 삭제
// 일반적으로 보기 쉽게 24*60*60 이런식으로 시분초 단위로 함
```
4. hidden input (추후에 함)  
5. querystring (추후에함)  
#### Application과 Session, Cookie의 차이점  
```
- Application에 저장을 한다 -> 전역범위에서 쓰는 값, 생명주기는 WAS와 같음, 존재하는 위치는 서버쪽에 자원을 쓰기 때문에 WAS 서버의 메모리이다. 
- Session에 저장을 한다 -> 서버 자원을 사용하되, 생명주기는 세션이 시작해서 종료될 때 까지,  저장위치는 WAS 서버의 메모리이다.
- Cookie에 저장을 한다 -> 웹 브라우저별 지정한 Path 범주에 저장, 브라우저에 전달한 시간부터 만료시간만큼 보관, 웹 브라우저에 저장됨.
// 보관기간이 길어질 경우 쿠키로 저장하면 됨, 특정 URL에서만 쓰게되면 쿠키의 Path를 사용하여 쿠키를 사용하는게 바람직함.
```


## 웹 페이지를 전환하는 법  
```java
response.sendRedirect("이동할 웹 사이트 주소");
```

## Servlet에서 Javascript 실행하는법  
```java
ScriptEngine engine = new ScriptEngineManager().getEngineByName("nashorn"); // nashorn은 엔진이름임 js엔진도 있음
engine.eval("5+3"); // Javascript 실행문 반환형 Object
```

## 쿠키를 삭제하는 방법  
```java
Cookie c = new Cookie("키","값");
c.setMaxAge(0);
// 쿠키수명을 0으로 변경
```

## Get요청과 Post요청에 특화된 함수 service  
1. service() 메서드에서 Get과 Post를 구분하는 방법  
```java
@Override
protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
	// TODO Auto-generated method stub
	// service 에서 request method를 확인하는 방법
	if(req.getMethod().equals("GET")) { // 반드시 대문자
		System.out.println("Get요청이 왔습니다.");
	}else if(req.getMethod().equals("POST")) {
		System.out.println("Post요청이 왔습니다.");
	}
}
```
2. doGet(), doPost() 메서드를 오버라이딩하는 방법  
```java
@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
	// TODO Auto-generated method stub
	System.out.println("doPost메서드가 호출되었습니다.");
}
```

##### service 의 super.service(req, res) 가 실행되면 doGet 혹은 doPost가 실행됌  
##### 전처리(라우팅) 할 수있게 만들어놓은거 같음  
##### doGet과 doPost를 실행하는 것이 service 메서드의 super.service(req,res);  


## HTML 출력이 많은 결과 페이지, 복잡한 HTML 페이지를 보낼 때 Servlet으로 작성하면 코드가 길어짐 -> 그래서 Jasper(JSP:Java Servlet Page)을 사용  
- jsp는 톰캣이 index.jsp파일을 ~~~ 톰캣경로/index_jsp.java 파일로 변환  

## JSP의 코드블록 종류  
```java
1. 코드블록, 기본 실행
 ->    <%  
    int a = 0;
    int c = 3;
 %>
2. 코드블록,출력
 ->    <%= x+y %>
3. 선언부(Declaration)
 -> <%!  
  public static void add(){
  	// ....
  }
  %>
4. 지시 블럭, 초기 설정을 위한 Page 지시자
  -> <%@ page langiage="java" contentType="text/html;charset=UTF-8" @>
```   

## JSP의 내장객체  

```java
PageContext pageContext;
HttpSession session;
ServletContext applitaion;
ServletConfig config;
JspWriter out;
Object page;
JspWriter _jspx_out;
PageContext _jspx_page_context;
```
브랜치 테스트
