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
  
## ContentNegotiatingViewResolver
- 자기가 직접 뷰 이름에 해당하는 뷰를 찾아주는게 아니라 다른 ViewResolver들에게 위임함, 가장 적절한 ViewResolver를 찾아줘서 유연함0
