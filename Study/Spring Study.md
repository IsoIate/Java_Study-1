  # 스프링 프레임워크 입문 (백기선님)
  ## IoC (Inversion Of Control)  
  #### IoC  
   - 주로 의존성에 대한 컨트롤이 뒤바꼈다라고 생각하면 될듯
   - 원래의 의존성은 자기 자신이 가지고 있음
   ```java
    class OwnerCOntroller{
        private OnwerRepository repository = new OwnerRepository();
    }
   ```
   - 위의 코드를 보면 OwnerRepository가 있어야 OwnerController를 제대로 사용할 수 있음, 즉 OwnerController는 OwnerRepository가 필요로 함
   - 그래야만 OwnerController의 모든 기능을 정상적으로 사용할 수 있음
   - 그 OwnerRepository의 의존성(객체 생성)을 누가 만드느냐, 누가 관리하느냐 가 문제
   - 의존성을 바꾼게 IoC, 나 이외에 누군가가 의존성을 넣어주는 것
   - 객체를 직접 생성을 하는 것이 아니라 IoC Container(ApplicationContext)로부터 주입을 받는 것  
   - 테스트 작성하기가 한결 쉬워짐 (Mock객체..), 테스트 객체를 주입받아서 테스팅을한다면 한결 쉬워짐   
  #### IoC 컨테이너
   - ApplicationContext와 BeanFactory가 가장 핵심적인 인터페이스  
   - 빈(Bean)을 만들고 엮어주며 제공하는 기능을 가지고 있음 (의존성 관리)  
   - @Controller, @Componemt, @Repository, @Service... 등이 붙어있으면 bean으로 등록을 하고 관리함
   - ApplicationContext는 빈들을 만들고 만든 그 빈들의 의존성을 엮어준다
  #### 빈(Bean)  
   - Bean은 스프링 IoC 컨테이너가 관리하는 객체이다.
   - Bean을 등록하는 방법
   ```java
    1. Component Scanning
      @Component -> @Controller
                 -> @Repository
                 -> @Service
    2. XML이나 자바 설정 파일에 빈을 등록
   ```
   - 꺼내쓰는 방법은 @Autowired, @Inject 또는 ApplicationContext에서 getBean()으로 직접 꺼내는 방법
  #### 의존성 주입(Dependency Injection)  
   - @Autowired / @Indect 를 붙이는 위치
   ```
    - 생성자
    - 필드
    - Setter
   ```
   - 어떠한 빈이 되는 클래스에 생성자가 오직 하나만 있고 그 생성자의 매개변수 타입이 빈으로 등록이 돼있다면 그 빈을 주입을 해준다. Autowired가 없더라도...( 스프링5, 스프링 부트2? 에서 추가된 기능인듯 )  
   - DI를 하는 가장 베스트는   
   1. 생성자
   2. Setter가 있다면 Setter
   3. Setter가 없다면 필드
    
  ## AOP (Abstract Object Programming)
  ### AOP
   - Logging 혹은 Transaction, 성능측정, 인증, 권한 확인 등 실제 서비스하는 객체의 기능 앞면, 뒷면에 선/후 작업을 하는 것
   - 바이트 코드를 조작하거나, Proxy패턴을 사용하여 작성
   - Spring AOP는 Proxy를 사용하여 구현
   ```java
   // ex) 
     public to(){
        AAAA
        실행문
        BBBB
     }
   ```
  ## PSA(Portable Service Abstract) -> 추상화  
  #### PSA  
   - 대부분의 스프링 코드들이 다 PSA으로 구현되어있음  
   - 추상화의 극한?이라고 생각하면 될거같음, 표현하자면 아주 잘 만든 인터페이스  
   - PSA를 사용하면 조금 더 유연하고 확장성 좋은 코드를 만드는 것이 가능  
  #### 스프링 캐시
   - @EnableCashing -> 스프링 프레임워크에서 온 어노테이션
   - 캐시 관련된 기능이 활성화, @Cacheable, @CacheEvict 어노테이션들을 사용할 수 있는데 반드시 CacheManager가 있어야한다.
   - Transactional이랑 똑같음, 그 어노테이션을 처리하는 Aspect가 있음, 그 Aspect에서는 반드시 TranscsationManager를 사용함
   
  #### 스프링 MVC  
   - @Controller와 @GetMapping을 사용하여 웹MVC를 구현하므로 그러므로 요청하는 플랫폼이 Servlet 이거나 Reactive 더라도 상관이 없다.  
   - 즉 웹 구현체를 독립적으로 만들어주는게 주 목적  
   - PSA의 가장 큰 목표, 장점 -> 밑단에 있는 코드가 바뀌어도 내 코드가 바뀌지 않음  
   ```spring
    @Controller
    class OwnerController {
        .....
        @GetMapping("/owners/new")
        public String initCreationForm(Map<String, Object> model) {
          Owner owner = new Owner();
          model.put("owner", owner);
          return VIEWS_OWNER_CREATE_OR_UPDATE_FORM;
        }
    }
    // 밑에 View를 해주는 플랫폼이 Servlet이나 Reactive등으로 바뀌어도 Spring Code는 바뀌지 않음
   ```
