# 스프링 프레임워크 핵심 기술 (백기선님)
## 스프링이란?
__소규모 애플리케이션 또는 기업용 어플리케이션을 자바로 개발하는데 있어 유용하고 편리한 기능을 제공하는 프레임워크__
 - 스프링의 역사
  - 2003년 등장
  - 최근까지 주로 서블릿 기반 애플리케이션을 만들 때 사용해 옴
  - 스프링 5부터는 WebFlux 지원으로 서블릿 기반이 아닌 서버 애플리케이션도 개발할 수 있게 됨 
# IoC 컨테이너와 빈
#### 스프링 IoC 컨테이너
 - 여러개발자들이 스프링 커뮤니티에서 논의해서 만들어 낸 여러가지 DI 방법과 Best Practice 들이, 노하우가 쌓여있는 프레임워크
 - Conatainer라고 부르는 이유는 빈들을 담고 있기 때문에... 
 - 초기에는 xml을 사용한 DI를 사용하였지만 요즘엔 Annotation을 사용하여 빈으로 등록함
 - BeanFactory가 핵심
#### 빈
 - 스프링 IoC 컨테이너가 관리 하는 객체  
 - Bean의 Scope을 관리하기 위해서도 IoC컨테이너를 사용하면 편리함  
 - 스프링IoC 컨테이너가 관리한는 빈들은 기본적으로 싱글톤으로 관리 (Singleton scope)
 ##### Scope
  - 싱글톤 : 하나
  - 프로토타입 : 매번 다른 객체
 - 빈으로 등록할 때 아무런 어노테이션을 달지 않았을 경우 싱글톤 스코프로 등록이 됌
 - 이미 생성한 빈을 주기 때문에 매번 만드는 프로토타입에 비해서 성능적으로 유리
 ##### 라이프사이클 인터페이스 지원
  - 어떤 빈이 만들어졌을 때 추가적인 작업을 할 경우...
  ```java
  @PostConstruct
  public void postConstruct() {
      System.out.println("====================");
      System.out.println("Hello");
  } 
  ```
 ##### Autowired
 - 생성자에 다는 법 (Spring 4.3부터는 생략가능)
 ```java
 @Service
 public class BookService {

    private BookRepository bookRepository;

    @Autowired
    public BookService(BookRepository bookRepository){
        this.bookRepository = bookRepository;
    }

 }
 ```
 - setter에 다는 법
 ```java
  @Service
  public class BookService {

      private BookRepository bookRepository;

      @Autowired
      public void setBookRepository(BookRepository bookRepository) {
          this.bookRepository = bookRepository;
      }
  }
 ```
 - @Autowired를 붙인 빈이 없지만 setter같은 경우는 클래스를 생성하는데 문제가 없을것 같지만... @Autowired가 있으므로 반드시 빈을 생성하게 된다. 그래서 빈이 없을때 에러가 발생하게 됩니다.
 - DI를 선택적으로 하게 하려면 @Autowired(required = false)를 설정해주면 된다.. (default값은 true)
 ```java
 @Service
 public class BookService {

     private BookRepository bookRepository;

     @Autowired(required = false)
     public void setBookRepository(BookRepository bookRepository) {
         this.bookRepository = bookRepository;
     }
 }
 ```
 - 빈이 여러개일 경우...
 ```java
 @Repository
 class Book1 implements BookRepository{}
 
 ...
 
 @Repository
 class Book2 implements BookRepository{}
 
 ...
 
 @Service
 class BookService{
  
   @Autowried
   BookRepository bookRepository;
 }
 ```
 - 에러 발생 2가지의 해결 방법
  1. 자주 사용하는 빈에 @Primary 붙이기 -> @Primary가 붙은 빈을 우선적으로 주입
  ```java
  @Repository @Primary
  class Book1 implements BookRepository{}
 ```
  2. @Qualifier 붙이기 -> @Qualifier("빈의 아이디") 빈의 아이디를 쓰면 됌
  3. 빈 이름을 지정해서 받기 (비추) (스프링이 빈 아이디를 보기때문에)
   - BeanPostProcesser에 의하여 동작, Bean의 인스턴스를 만든 다음에 빈의 인스턴스를 만드는 initialization 라이프 사이클 이전, 이후에 어떤 부가적을 작업을 할 수 있는 콜백이 BeanPostProcesseor이다.
  ```java
  @Service
  public class BookService {
      @Autowired
      BookRepository seungJunBookRepository;

      public void printBookRepository(){
          System.out.println(seungJunBookRepository.toString());
      }
  }
  ```
  4. 모든 빈을 다 받아버리기
  ```java
  @Service
  public class BookService {

      @Autowired
      List<BookRepository> bookRepository;

      public void printBookRepository(){
          this.bookRepository.forEach(System.out::println);
      }
  }
  ```
  ###### Component Scan과 Component  
  - @ComponentScan는 3.1부터 출시
  - 가장 중요한건 basePackages 하지만 Type-safe하지않아서 BasePackageClasses를 사용
  - SpringBootApplication 어노테이션이 붙은 경로부터 하위경로까지 모두 스캔
  - ExecludeFilter 설정가능 > 기본으로 AutoConfigurationExcludeFilter,  TypeExcludeFilter 타입을 모두 걸러냄
  - __@Component__
   - @Repository
   - @Service
   - @Controller
   - Configuration
   - __펑션을 사용한 빈 등록__
   ```java
        SpringApplication app = new SpringApplication(Demospring51Application.class);
        app.addInitializers((ApplicationContextInitializer<GenericApplicationContext>) cxt -> {
            cxt.registerBean(MyService.class);
            cxt.registerBean(ApplicationRunner.class, ()  -> args1 -> System.out.println("Functional Bean Definition"));
        });
        app.run(args);
   ```
  ###### 빈의 스코프
  - 싱글톤
  - 프로토타입
   - Request
   - Session
   - WebSocket
  - 프로토타입 빈이 싱글톤 빈을 참조하면 문제 없음
  - 하지만 싱글톤 빈이 프로토 타입의 빈을 참조하면?
   - 프로토타입빈을 가져올때마다 새로 생성하기때문에 변경이 안됨
  - 싱글톤 객체 사요여시 주의할 점 -> 프로퍼티를 공유하기때문에 
    - scoped-proxy
    ```java
     @Component @Scope(value = "prototype", proxyMode = ScopedProxyMode.TARGET_CLASS)
     public class Proto {

         @Autowired
         Single single;
     }
    ```
    - Object-Provider
    ```java
     public class Single {

       @Autowired
       private ObjectProvider<Proto> proto;

       public Proto getProto() {
           return proto.getIfAvailable();
       }
     }
     ```
 ###### EnviromentCapable의 프로파일  
  - 프로파일 -> 빈들의 묶음, 환경(테스트환경, 프로덕션환경 등...) 개발용 서버... 프로덕션용 서버... 각각의 환경에 따라 다른 빈을 써야하는경우, 특정 환경에서만 어떠한 빈을 등록해야하는 경우 프로파일이라는 기능이 추가됨
  - Enviroment 인터페이스
   -VM option 에 -Dspring.profiles.active="..." 혹은 Run config에 Active Profiles... 에 등록
   - 혹은 Annotation으로 @Profile("profile명")  
 ###### Enviroment의 프로퍼티
  - 어플리케이션에 등록되어있는 키 밸류성의 프로퍼티에 접근
  - 프로퍼티는 다양한 방법으로 정의할 수 있는 설정 값
  - 우선순위가 있음
  ```
  StandardServletEnvironment의 우선순위
   1.ServletConfig 매개변수
   2. ServletContext 매개변수
   3. JNDI (java:comp/env/)
   4. JVM 시스템 프로퍼티 (-Dkey="value")
   5. JVM 시스템 환경변수 (운영체제환경변수)
  ```
 ###### MessageSource
   - 국제화(i18n) 기능을 제공해주는 인터페이스\
   - 스프링 부트를 사용한다면 별다른 설정 필요없이 messages.properties 사용할 수 있음
     - messages.properties
     - message_ko_KR.properties
     - ...
 ###### AplicationEventPublisher
  - 이벤트 기반 프로그래밍에 필요한 인터페이스 제공, 옵저버 패턴 구현체
  - Event 기반의 프로그래밍 할 때 유용한 인터페이스
  - 사용할 클래스의 메서드에 @EventListener를 추가
  - 순서는 @Order(Ordered.....), 비동기는 @Async인데 MainApplication에 @EnableAsync를 달아줘야함
# 리소스  
### Resource 추상화  
 - java.net.URL을 추상화 한 것.
 - 스프링 내부에서 많이 사용하는 인터페이스
#### 추상화를 한 이유?
 - 클래스패스 기준으로 리소스를 읽어오는 기능 부재
 - ServletContext를 기준으로 상대 경로로 읽어오는 기능 부재
 - 새로운 핸들러를 등록하여 특별한 URL 접미사를 만들어 사용할 수 는 있지만 구현이 복잡하고 편의성 메소드가 부족하다.
 - 지금까지 사용해왔던것들... FileSystemXmlApplicationContext(), ClassPathXmlApplicationContext() -> 파일 시스템기준, 클래스패스 기준으로 사용
 - 리소스는 읽어올 때 ApplicationContext의 타입에 따라 결졍 되는데 ClassPathXmlApplciationContext 타입이라면 경로가 자동으로 classpath:기준이 된다.
 - 반대로 FileSystemXmlApplicationContext를 사용한다면 자동으로 ```file:///``` 기준으로 된다.
 - 되도록 접두어를 사용해서 가져오는걸 추천 (명시적)
 ```java
 ex)
 classpath:me/whiteship/config.xml
 file:///me/whiteship/config.xml
 ```
# Validation

# 데이터 바인딩

# SpEL

# 스프링 AOP

# Null-Safety
