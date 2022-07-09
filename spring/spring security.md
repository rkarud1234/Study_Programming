# Spring Security란 ?
Spring Security는 authentication, authorization과 일반적인 공격에 대한 보호를 제공하는 프레임워크이다.     
인터셉터의 기능과 헷갈릴 수 있는데 인터셉터는 사용자가 특정 URI로 요청 보냈을 때 컨트롤러로 가는 요청을 말그대로 인터셉트하는 것 뿐이다.     
> 예를 들어 회원만 접근할 수 있는 페이지에서 비로그인한 사용자가 요청을 보냈을 때 가로채서 로그인 페이지로 보내버리는 것.       
> 인터셉터가 인증 기능을 별도로 제공하지 않기 때문에 사용자 인증 관련 기능은 개발자가 직접 구현해야 함      

## Spring Security 사용하기     
Spring Boot 프로젝트에서 Spring Security를 사용하기 위해서는 아래와 같은 방법들을 사용할 수 있다.
1. [Spring 홈페이지](https://start.spring.io/)나 IDE(Eclipse, InteliJ, ...)를 통해 Dependency를 추가하는 방법
2. Maven으로 Dependency를 추가하는 방법     
  *pom.xml - dependency 추가*     
  ````xml
  <dependencies>
    <!-- ... other dependency elements ... -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
  </dependencies>
  ````
  Spring Boot는 BOM을 통해 버전을 관리하기 때문에 별다른 버전 설정이 필요 없지만, 원하는 버전을 사용하기 위해서 덮어씌울 수 있다.     
  *pom.xml - version 변경*     
  ````xml      
  <properties>
    <!-- ... -->
    <spring-security.version>5.7.2</spring-security.version>
  </properties>
  ````
  *pom.xml*에 설정을 통해 다양한 버전을 사용할 수 있다.([설명](https://unordinarydays.tistory.com/136))       
  ````xml
  <repositories>
	  <!-- ... possibly other repository elements ... -->
	  <repository>
		  <id>spring-snapshot</id>
		  <name>Spring Snapshot Repository</name>
		  <url>https://repo.spring.io/snapshot</url>
	  </repository>
  </repositories>
  ````

`gradle`로도 설정 가능하니 [`docs`](https://docs.spring.io/spring-security/reference/getting-spring-security.html)를 참조하도록 하자


## Spring Security - Authentication
Sprint Security는 authentication(인증)에 대한 포괄적인 기능을 제공한다.      
authentication이란 해당 페이지에 접속하려는 사람이 누구인지, 접근 권한이 있는지 판단하는 것을 의미한다.     
일반적인 인증의 방식으로는 id와 pw를 이용해 로그인하는 방법이 있다.      
로그인 등을 통해 해당 사용자가 인증되면 기능을 제공할 수 있게 된다.      




