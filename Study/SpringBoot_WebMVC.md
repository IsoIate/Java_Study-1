# SpringBoot Web MVC
## Spring Web MVC를 설정 없이 사용할 수 있는 이유?  
 - SpringBoot가 의존성으로 받은 springbootautoconfigure이라고 하는 모듈의 spring.factories 라는 설정파일에 WebMvcAutoConfiguration를 로드하고 있다.  
 - WebMvcAutoConfigruation에는 여러 어노테이션이 있어서 WebMvc개발의 설정을 도와준다.  
## HiddenHttpMethodFilter  
 - 스프링 3.0 에서부터 지원하는 필터 (스프링부트에서 온것 아님)  
 - put 이나 detele,patch 요청일 경우에 _method라는 히든 폼 파라미터로 어떤 메서드인지 받아와서 컨트롤러에 매핑을 시켜줌  
## HttpPutFormContentFilter  
 - Http Post 혹은 Form 데이터를 보낼 수 있게 서블릿 스펙이 명시돼어있음  
 - put이나 patch도 application/x-www-form-urlencoded라는 요청으로 폼 데이터를 보내오면 POST요청에서 꺼낼 수 있게 매핑을 해줌  
## 스프링 부트가 지원하는 WebMVC의 기능을 다 사용하면서 추가적인 기능을 구현하고싶을 때  
 - 다음과 같이 어노테이션을 붙히고 WebMvcConfigurer을 구현하면 됌  
 - 스프링 MVC확장 -> @Configuration + WebMvcConfigurer  
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
  ...
}
```
 - 스프링 MVC 재정의 -> @EnableWebMvc  
```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {
  ...
}
```
## HttpMessageConverters
- HTTP요청 본문을 객체로 변경하거나, 객체를 HTTP 응답 본문으로 변경할 때 사용  
```
ex ) {"username":"seungjun", "password":"123"} <-> User
 - @ResquestBody
 - @ResponseBody
```
```java
@PostMapping("/user")
public @ResponseBody User create(@RequestBody User user){
  // 스프링이 알아서 Request 본문을 객체로 변환해줌\
  return null;
}
```
 - @RestController 어노테이션이 붙어 있으면 @ResponseBody르 생략해도 됌
## MessageConverter
 - 요청에 따라 HttpMessageConverter타입이 바뀜, 만약 json으로 메세지를 보낸다면 JsonMessageConverter를 사용
 - HttpMessageConverter가 객체를 내보낼 떄 (User같은), 기본적으로 JsonMessageConverter가 사용된다.
```java
@PostMapping("/users/create")
public User create(@RequestBody User user){
    return user;
}
//Test
@Test
public void createUser_JSON() throws Exception {
    String userJson = "{\"username\":\"seungjun\", \"password\":\"123\"}";
    mockMvc.perform(post("/users/create")
              .contentType(MediaType.APPLICATION_JSON_UTF8)
              .accept(MediaType.APPLICATION_JSON_UTF8)
              .content(userJson))
          .andExpect(status().isOk())
          .andExpect(jsonPath("$.username", is(equalTo("seungjun"))))
          .andExpect(jsonPath("$.password", is(equalTo("123"))));
    }
```
## ViewResolver
### ContentNegotiatingViewResolver란?
- ViewResolver중에 하나인데, 들어오는 요청에 accept Header에 따라 응답이 달라짐 (json, text, html 등..)
- acceptHeader가 없는 경우 /path?format=pdf.. 이런식으로 요청을 받아 타입을 추론함
### HttpMediaTypeNotAcceptableException
 - HttpMediaType을 처리할 HttpMessageConverter가 없는 것
 - XML을 내보내려고 할 때 의존성 추가
 - HttpMessageConverterAutoConfiguration에서 추가된 Configuration들 중에 MappingJackson2HttpMessageConverterConfiguration이 있기 때문에 등록된 맵핑을 하려면 다음과같이 jackson 의존성을 추가해야함
```
<dependency>
     <groupId>com.fasterxml.jackson.dataformat</groupId>
     <artifactId>jackson-dataformat-xml</artifactId>
     <version>2.9.6</version>
</dependency>
```
## 정적 리소스 지원
- 정적 리소스 맵핑 "/**"
- 기본 리소스 위치
 - classpath:/static
 - classpath:/public
 - classpath:/resources/
 - classpath:/META-INF/resources
- application.properties에서 변경
 - spring.mvc.static-path-parttern : 매핑 설정 변경 가능
 - spring.mvc.static-locations: 리소스 찾을 위치 변경 가능
- Last-Modified 헤더를 보고 304 응답을 보냄
- ResourceHttpRequestHandler가 처리함
 - WebMvcConfigurer의 addResourceHandlers로 커스터마이징 가능
 ```java
 @Override
 public vcoid addREsourceHandlers(ResourceHandlerRegistry registry){
    registry.addResourceHandler("/m/**")
        .addResourceLocations("classpath:/m/")
        .setCachePeriod(20);
 }
 ```
## 웹 JAR
- 웹 JAR란 클라이언트에서 사용하는 자바스크립트 라이브러리같은 것을 JAR파일로 의존성을 받아서 css 혹은 html, javascript에서 참조할 수 있음
- 웹 JAR맵핑 "/webjars/**" 
```
<dependency>
      <groupId>org.webjars.bower</groupId>
      <artifactId>jquery</artifactId>
      <version>3.2.1</version>
</dependency>
```
```
<script src="/webjars/jquery/3.2.1/dist/jquery.min.js"></script>
<script type="text/javascript">
    $(function(){
        alert("ready!!");
    });
</script>
```
- 모듈의 버전을 생략하고 사용하려면, webjars-locator-core의존성을 추가하면 됌
```
<dependency>
      <groupId>org.webjars</groupId>
      <artifactId>webjars-locator-core</artifactId>
      <version>0.35</version>
</dependency>
```
```
<script src="/webjars/jquery/dist/jquery.min.js"></script>
<script type="text/javascript">
    $(function(){
        alert("ready!!");
    });
</script>
```
## index 페이지와 파비콘
- root를 요청했을 때 웰컴 페이지를 보여주기  
 - index.html 찾아 보고 있으면 제공  
 - index.템플릿 찾아보고 있으면 제공  
 - 둘 다 없으면 에러페이지  
- 파비콘이란 브라우저 title 옆에 있는 아이콘  
 - favicon.ico  
 - 파비콘 만들기 [https://favicon.io/](https://favicon.io/)  
## Thymeleaf  
- 스프링 부트가 자동 설정을 지원하는 템플릿 엔진  
- 템플릿 엔진이란 주로 View를 만들고, Code Generation, Email Template 제공  
 - Freemarket  
 - Groovy  
 - __Thymeleaf__  
 - Mustache  
- JSP를 권장하지 않는 이유  
 - JAR 패키징 할 때는 동작하지 않고 WAR 패키징을 해야만 동작  
 - Undertow는 JSP를 지원하지 않음  
- __Thymeleaf 사용하기__  
 - 의존성 추가 : spring-boot-starter-thymeleaf 
 - 템플릿 파일 위치 : /src/main/resources/template  
- JSP는 테스트가 어려움, Servlet Engine을 사용하기 때문...

## HtmlUnit
- HTML을 단위테스트하는 모듈
- 의존성 추가
```
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>htmlunit-driver</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>net.sourceforge.htmlunit</groupId>
            <artifactId>htmlunit</artifactId>
            <scope>test</scope>
        </dependency>
```
## ExceptionHandler
- 스프링 @MVC 예외 처리 방법
 - @ControllerAdvice
 - @ExchangepHandler
- 스프링 부트가 제공하는 기본 예외 처리기
 - BasicErrorController
  - HTML과 JSON응답 지원
 - 커스터마이징 방법
  - ErrorController 구현
### __쉽게 Error페이지 만들기__
- resources/static|template/error/ 에 404.html 혹은 5xx.html 등 에러코드 이름이 있으면 ErrorViewResolover가 돌려줌
## Spring HATEOAS
- __H__ ypermedia __A__ s __T__ he __E__ ngine __O__ f __A__ pplication __S__ tate  
- 서버 : 현재 리소스와 연관된 링크 정보를 클라이언트에게 제공한다.
- 클라이언트 : 연관된 링크 정보를 바탕으로 리소스에 접근한다.
- 연관된 링크 정보
 - Relation
 - Hypertext REference
## CORS
- 한 호스트에서 다른 포트 접속이 불가능한데 그걸 해결하는 듯..? 예를들면 18080포트를 사용하여 웹 서버를 구동한 뒤 똑같은 호스트에서 8080포트 웹서버로 Ajax통신을 보내보는것?
- 원래대로라면 Single-Origin Policy 때문에 구동 불가, 하지만 Cross-Origin Resource Sharing을 사용한다면 가능
- SOP과 CORS
 - Single-Origin Policy
 - Cross-Origin Resource Sharing
 - Origin??
   - URI 스키마 (http, https)
   - hostname (whieship.me, localhost)
   - 포트 (8080, 18080)
## RestController 과 Controller의 차이
 - @Controller는 주로 View를 반환하기 위해 사용합니다. 아래와 같은 과정을 통해 Spring MVC Container는 Client의 요청으로부터 View를 반환합니다.  
 - @RestController는 Spring MVC Controlle에 @ResponseBody가 추가된 것입니다. 당연하게도 RestController의 주용도는 Json 형태로 객체 데이터를 반환하는 것입니다.  
 
## Dispatcher-Servlet 이란?
 - Servlet Container에서 HTTP프로토콜을 통해 들어오는 모든 요청을 프레젠테이션 계층의 제일앞에 둬서 중앙집중식으로 처리해주는 프론트 컨트롤러(Front Controller)  
 - 클라이언트로부터 어떠한 요청이 오면 Tomcat과 같은 서블릿컨테이너가 요청을 받는데, 이때 제일 앞에서 서버로 들어오는 모든 요청을 처리하는 *프론트 컨트롤러*를 Spring에서 정의하였고, 이를 Dispatcher-Servlet이라고 한다. 그래서 공통처리작업을 Dispatcher 서블릿이 처리한 후, 적절한 세부 컨트롤러로 작업을 위임해줌.
 - 물론 Dispatcher-Servlet이 처리하는 url 패턴을 지정해주어야 하는데 일반적으로는 ```/*.do```,와 같이 /로 시작하며, .do 로 끝나는 url 패턴에 대해서 처리하라고 지정해준다.
## Dispatcher-Servlet의 장점
 - SpringMVC는 DispatcherServlet이 등장함에 따라 web.xml의 역할을 상당히 축소시켜주었다. 기존에는 모든 서블릿에 대해 URL 매핑을 활용하기 위해서 web.xml에 모두 등록해주어야 했지만, dispatcher-servlet이 해당 어플리케이션으로 들어오는 모든 요청을 핸들링 해주면서 작업을 상당히 편리하게 할 수 있게 되었다. 그리고 이 서블릿을 이용한다면 @MVC 역시 사용할 수 있게되어 좋다.
 
## Dispatcher-Servlet 구조
 ![Dispatcher-Servlet](https://github.com/eggme/Java_Study/blob/master/img/SpringMVC%20Structure.PNG)  
 
- Dispatcher-Servlet의 구조는 효율적으로 보이나, 모든 요청을 처리하다보니 img, js, css 파일 요청 등 Dispatcher-Servlet이 가로채는 현상이 발생됩니다. 이에 대한 해결책은
```
1. /apps 의 URL로 접근하면 Dispatcher Servlet이 담당한다.
2. /resources 의 URL로 접근한다면 Dispatcher-Servlet이 컨트롤 할 수 없으므로 담당하지 않는다.
```
- 이러한 방식은 코드가 상당히 지저분해져서 직관적인 설계가 될 수 없다. Spring은 이러한 문제를 해결함과 동시에 편리한 방법을 제공해주는데, 그것은 바로 ```<mvc:resources />```를 이용한 방법이다.
- 만약 DispatcherServlet에서 해당 요청을 찾을 수 없다면, 2차적으로 설정된 경로에서 요청을 탐색하여 자원을 찾아내는 방법이다. -> Resource 추상화?
 
