## 스프링 DI와 AOP  
#### DI -> Dependency Injection -> 의존 관계 주입  
프로그래밍에서 의존성(Dependency)이란, 어떤 클래스가 자신의 임무를 다하기 위해 필요한 값(필드 값)이나 사용할 다른 클래스와의 관계를 말한다.  
주입(Injection)이란, 어떤 클래스의 인스턴스에 대해 외부로부터 '의존성'을 설정하는 것을 말한다.  
의존 관계 주입 컨테이너가 하는 역할은 어떤 클래스가 필요로 하는 값이나 인스턴스를 생성, 취득하고, 그 클래스의 인스턴스에 대해 설정하는 것이다.  
스프링은 두 가지 주입을 지원합니다.  
 - 생성자를 통한 주입  
 - 설정 메서드를 통한 주입  
  
다음과 같은 Foo 클래스가 있고 필드로서 Bar에 대한 인스턴스 참조를 가지고 있습니다. 바꿔말하면 Foo 클래스가 Bar 클래스에 의존하고 있고, Bar에 대한 인스턴스 참조는 의존 관계라고 생각할 수 있습니다.
```java
public class Foo {
    private Bar bar;
}
```
##### 생성자를 통한 주입  
```java
public class Foo {
    private Bar bar;
    public Foo(Bar bar){
        this.bar = bar;
    }
}
```
##### 설정 메서드를 통한 주입  
```java
public class Foo {
    private Bar bar;
    public void setBar(Bar bar){
        this.bar = bar;
    }
}
```

##### 스프링 설정 파일 (beans.xml)  
```xml
<bean id="messageBean" class="sample1.MessageBeanImpl">
    <constructor-arg> <!-- 생성자를 통한 주입 -->
        <value>Spring</value>
    </constructor-arg>
    
    <property name="greeting"> <!-- 설정 메서드를 통한 주입 -->
        <value>Hello, </value>
    </property>
    
    <property name="outputter">
        <ref local="outputter" />
    </property>
</bean>
<bean id="outputter" class="sample1.FileOutputter">
    <property name="filePath">
        <value>out.txt</value>
    </property>
</bean>
```
### constructor-arg요소와 property 요소에 대해
 - constructor-arg와 property 요소는 다음과 같은 속성이 있다. 단 property 요소는 ref 속성과 value 속성을 가진다.  
 ```
 속성                   설명
 ------------------------------------------------------------------------------------------------------
 index                  생성자의 몇 번째 인수에 값을 넘길 것인가를 지정한다.
 type                   생성자의 어떤 데이터 타입인 인수에 값을 넘길 것인지 지정한다.
 ref                    자식 요소 <ref bean="Bean 이름" /> 대신 사용할 수 있다.
 value                  자식 요소 <value>값</value>대신 사용할 수 있다.
 ------------------------------------------------------------------------------------------------------
 ```
 ###### 아래와 같이 생성자를 통해 주입하는 경우
 ```java
 Foo(int a, String b){...}
 ```
 ```xml
 <bean id="foo" class="Foo">
    <constructor-arg index="0" value="25" />
    <constructor-arg index="1" value="Spring" />
 </bean>
 ```
 ###### 타입을 지정해서 보낼 수도 있음
 ```xml
  <bean id="foo" class="Foo">
    <constructor-arg type="int" value="25" />
    <constructor-arg type="java.lang.String" value="Spring" />
 </bean>
 ```
 ###### Null 값을 넣어야 할 때
 ```xml
  <constructor-arg>
    <value></value> <!-- 다음과 같이 하면 "" -> 공백 문자열이 입력됨 -->
  </constructor-arg>
-------------------------------------------------------------------------------
  <constructor-arg>
    <null /> <!-- Null 값을 넣는 바람직한 방법 -->
  </constructor-arg>
```

### Bean 라이프 사이클  
의존 관계 주입 컨테이너에서 핵심인 BeanFactory가 하는 역할은 Bean의 생성과 관리이다.  
다음 예제는 BeanFactory에 의해 Bean이 생성되고, 그 Bean을 사용할 수 있기까지 호출되는 메서드를 확인하는 예제이다.  

###### 예제의 파일 구조
```
-project
   - build.xml
   - beans.xml
   - lib
   - src
     - sample1
        - HelloApp.java
        - MessageBean.java
        - MessageBeanImpl.java
        - CustomBeanPostProccesor.java
```

###### 스프링 설정 파일(beans.xml)  
```xml
<!-- Bean의 모든 프로퍼티 지정이 끝난 후 호출되는 메서드를 '사용자 구현 초기화 메서드' 라고 한다.
     init-method 속성에 사용자가 실행할 메서드 명을 지정하면 지정한 메서드가 실행된다.-->
<bean id="messageBean" class="sample1.MessageBeanImpl" init-method="init"> 
  <property name="greeting">
    <value>Hello, </value>
  </property>
</bean>
```

###### 소스 파일
```java
public class HelloApp {
   public static void main(String[] args) {
       XmlBeanFactory factory = new XmlBeanFactory(new FileSystemResource("beans.xml"));
       factory.addBeanPostProccessor(new CustomBeanPostProcessor());
       MessageBean bean = (MessageBean)factory.getBean("messageBean");
       bean.sayHello();
   }
}
```

```java
public class MessageBeanImpl implements MessageBean, BeanNameAware, BeanFactoryAware, InitializingBean, DisposableBean {
    private String greeting;
    private String name;
    private String beanName;
    private BeanFactory beanFactory;
    
    public MessageBeanImpl(){
       System.out.println("1. Bean의 생성자 실행");
    }
    
    public void setGreeting(String greeting){
       this.greeting = greeting;
       System.out.println("2. 설정 메서드 실행");
    }
    
    public void setBeanName(String beanName){
       System.out.println("3. Bean 이름지정");
       this.beanName = beanName;
       System.out.println(" ->" + beanName);
    }
    
    public void setBeanFactory(BeanFactory beanFactory){
       System.out.println("4. BeanFactory 지정");
       this.beanFactory = beanFactory;
       System.out.println(" ->" + beanFactory.getClass());
    }
    
    public void init(){
       System.out.println("7. 초기화 메서드 실행");
    }
    
    public void destory(){
       System.out.println("종료");
    }
    
    publci void afterPropertiesSet(){
       System.out.println("6. 프로퍼티 지정 완료");
    }
    
    public void sayHello(){
       System.out.println(greeting + beanName + "!");
    }
}
```
```java
public class CustomBeanpostProcessor implements BeanPostProcessor {
   @Override
   public Object postProcessBeforeInitialization(Object bean, String beanName){
      System.out.println("5. 초기화 전 Bean에 대한 처리 실행");
      return bean;
   }
   @Override
   public Object postProcessAfterInitialization(Object bean, String beanName){
      System.out.println("6. 초기화 후 Bean에 대한 처리 실행");
      return bean;
   }
}
```

###### 실행 순서
 1. Bean의 인스턴스화(생성자 호출)  
 2. 필드값 지정  
 3. setBeanName() 메서드 호출(BeanNameAware 인터페이스를 구현하고 있는 경우)  
 4. setBeanFactory() 메서드 호출(BeanFactoryAware 인터페이스를 구현하고 있는 경우)  
 5. BeanPostProcessor의 postProcessBeforeInitialization() 메서드 호출(BeanFactory에 BeanPostProcessor 클래스가 관련되어 있는 경우)  
 6. afterPropertiesSet() 메서드 호출(InitiailizingBean 인터페이스를 구현하고 있는 경우)  
 7. 사용자 구현 초기화 메서드 호출(사용자 구현 초기화 메서드를 정의하고 있는 경우)  
 8. BeanPostProcessor의 postProcessAfterInitialization() 메서드 호출(BeanFactory에 BeanPostProcessor 클래스가 관련되어 있는 경우)   
###### 컨테이너가 종료될 때
 1. destory() 메서드 호출(DisposableBean 인터페이스를 구현하고 있는 경우)  
 2. 사용자 구현소거 메서드 호출(사용자 구현소거 메서드를 정의하고 있는 경우)  


###### ApplicationContext 인터페이스
 - BeanFactory에 몇 가지 편리한 기능을 추가
 - 메시지 국제화, 자원 접근 수단 간소화, 이벤트 처리, 복수 컨텍스트 적재 기능 추가
```java
XmlBeanFactory factory = new XmlBeanFactory(new FileSystemResource("beans.xml"));
// factory.addBeanPostProcessor("new BeanName()"); ...
// 다음과 같이 변경
ApplicationContext factory = new FileSystemApplicationContext("beans.xml");
```
 - ApplicationContext는 BeanPostProcessor을 자동으로 읽어 들이기 때문에 addBeanPostProcessor()을 제거 하는 대신 beans.xml에 다음과 같은 Bean 정의를 추가
```xml
<bean class="sample1.CustomBeanPostProcessor" />
```
 - 결과적으로 코드는 다음과 같음
```java
public class HelloApp{
   public static void main(String[] args){
       ApplicationContext factory = new FileSystemApplicationContext("beans.xml");
       MesssageBean bean = (MessageBean)factory.getBean("messageBean");
       bean.sayHello();
   }
}
```
```xml
<beans xml...>
  <bean id="messageBean" class="sample1.MessageBeanImpl" init-method="init">
   <property name="greeting><value>Hello, </value></property>
  </bean>
  <bean class="sample1.CustomBeanPostProcessor" />
</beans>
```
```
※ 이 책에선 @Autowired 어노테이션을 설명하지 않음  
@Autowired의 어노테이션의 속성
required -> 의존관계 설정이 필수인지 여부를 true/false로 지정한다. 기본 값은 true.
```
 
