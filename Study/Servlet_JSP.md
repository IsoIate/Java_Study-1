# Servlet/JSP 강의 01 ~ 10
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

<pre>
<code>
Server.xml -> Host 안에

Context path="it" docBase = "C:\tools\apache-tomcat-9.0.36\webapps\ITWeb\it" privileged="true"

추가
</code>
</pre>
