# 스프링 데이터
## 인메모리 데이터베이스
- 지원하는 인메모리 데이터베이스
1. __H2 (추천)__
2. HSQL
3. Derby
- Spring-JDBC가 클래스패스에 있으면 자동 설정이 필요한 빈을 설정 해줍니다.\
 - DataSource 및 JDBCTemplate
```
인메모리 데이터베이스란?
 -> 
```
- 인메모리 데이터베이스 기본 연결 설정
```
databaseName -> testdb
username -> sa
password -> ""
```
## 스프링 부트 2.3부터 인메모리 DB설정이 매번 새로운 이름으로 만들어지도록 변경되서 testdb로 고정시키는 방법은 다음과 같다.
```
spring.datasource.generate-unique-name=false
```
## 스프링 DBCP, Mysql
```
DBCP란 ?
 -> DataBase Connection Pool
 -> 데이터베이스의 Connection을 만드는 과정에 많은 리소스가 필요해서 Connection을 미리 만들어 놓고, 어플리케이션이 필요할 때마다 가져다 쓰는 방식 Thread Pool와 같음
 -> DBCP가 어플리케이션 성능에 아주 핵심적인 요소로 작동
```
- 스프링이 지원하는 DBCP
1. __HikariCP (기본)__
2. TomcatCP
3. Commons DBCP2
- application.properties에 다음과 같이 추가하면 mysql 사용가능
```
spring.datasource.url=jdbc:mysql://localhost:3306/junstudio?useSSL=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=root
```
