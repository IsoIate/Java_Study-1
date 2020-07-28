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
