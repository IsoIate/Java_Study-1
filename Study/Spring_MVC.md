# SpringMVC

## @EnableWebMvc
- DelegatingWebMvcConfigruation Class를 import하는데 이 파일은 설정파일인데, 이 클래스가 상속받고있는 클래스가 WebMvcConfigurationSupport 클래스가 실질적으로 빈들이 매핑되어있음
- Interceptor들 관리
- 반드시 WebApplicationInitializer 를 구현하는 객체의 onStartup 메서드에서 Application.setServletContext() 메서드를 실행해야함, 그 이유는 @EnableWebMvc의 DelegatingWebMvcConfiguration의  WebMvcConfigurationSupport가 ServletContext를 종종 사용하기 떄문

## HandlerAdapter
- HandlerAdapter같은 경우는 MessageConverter를 사용해서 요청에 본문에 들어오는 데이터를 객체로 변환하거나 객체를 데이터로 변환시키는 작업을 함

## WebMvcConfigurer
- WebMvcConfigurer은 WebMvcConfigurationSupport를 커스터마이징 하면서 같은결과를 얻을 수 있는 인터페이스, 델리게이트 패턴으로 되있어서 원본에 영향이 없이 데이터를 수정할 수 있는듯
- prefix, surfix설정
```
@Configuration
@ComponentScan
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        registry.jsp("/WEB-INF/", ".jsp");
    }
}
```

# 스프링 부트의 MVC 설정
- 스프링 부트의 '주관'이 적용된 자동 설정이 동작한다.
  1. JSP 보다 Thymeleaf 선호
  2. JSON 지원
  3. 정적 리소스 지원 (+ 웰컴 페이지, 파비콘 등 지원)
  
  
## 왠만하면 application.properties를 통해서 커스터마이징을하는것을 추천!!!
## ContentNegotiatingViewResolver
- 자기가 직접 뷰 이름에 해당하는 뷰를 찾아주는게 아니라 다른 ViewResolver들에게 위임함, 가장 적절한 ViewResolver를 찾아줘서 유연함

## WebMvcConfigurer

## Fomatter
- 어떠한 객체를 문자열로 변환하거나, 문자열을 다른 객체로 변환시킬 수 있는 인터페이스
  - Printer : 해당 객체를 (Locale정보를 참고하여) 문자열로 어떻게 출력 할 것인가)
  - Parser : 어떤 문자열을 (Locale정보를 참고하여) 객체로 어떻게 변환 할 것인가)

## 도메인 클래스 컨버터 자동 등록
- Spring Data JPA가 지원하는 클래스, 어떤 아이디에 해당하는 도메인 클래스로 변환을 해주는 컨버터가 자동으로 등록이 됨

## 핸들러 인터셉터
- 핸들러 맵핑 : 어떠한 요청을 처리할 핸들러를 찾아주는 것
- 핸들러 맵핑에 설정하는게 인터셉터
- 설정을 해두면 핸들러 맵핑이 찾아주는 그 객체에 인터셉터들을 적용을 시켜줌
```
    // '/hello' 라는 요청 발생
    // preHandle -> 일반적인 서블릿 필터로 서블릿 요청이 처리되기 전에 뭔가를 하는 것과 비슷한데 조금 더 구체적으로 처리할 수 있음
    //              boolean preHandle(request, respoonse ,handler)
    //                      -> '핸들러'에 대한 정보도 제공이 되서, 서블릿 필터에 비해 보다 더 세밀한 로직을 구현 할 수 있음
    //                          리턴 타입이 boolean 이라서 다음 인터셉터 또는 핸들러로 요청, 응답을 전달할지(true) 끝낼지(false) 알린다.
    // 요청 처리
    // postHandle -> 뷰 랜더링하기 직전이라 modelAndView를 제공함, modelAndView 커스터마이징 가능
    //               void postHandle(request, response, modelAndView)
    //                      ->
    // 뷰 랜더링
    // afterCompletion -> 뷰 랜더링이 끝난 뒤에 호출
    //                    void afterCompletion(request, response, handler, ex)

    // vs 서블릿 필터
    //      -> 핸들러에 따라 특정 로직을 변경할 경우는 핸들러 인터셉터가 구현하는게 좋고
    //      -> 뭔가 전역적인, 스프링에 특화되어있는 정보와 관련이 없는 경우, 일반적인 경우로 기능을 구현하는 경우는 서블릿 필터가 올바르다
    /*
           postHandle과 afterCompletion은 역순으로 진행된다
           preHandle 1
           preHandle 2
           요청 처리
           postHandle 2
           postHandle 1
           뷰 랜더링
           afterCompletion 2
           afterCompletion 1
     */
```
