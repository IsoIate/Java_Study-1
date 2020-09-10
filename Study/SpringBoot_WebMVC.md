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






