---
toc: true
title: " MVC. JSP 에서 JS CSS 불러오기"
description: "JSP 에서 정적 resource (Javascript, CSS, Image 등등) 불러오기"
categories: [Spring]
tags: [MVC]
redirect_from:
  - /2019/01/10/
---

> JSP 파일 내에서 Javascript, CSS 등 정적 파일을 불러오는 법을 설명합니다.

### JSTL 라이브러리 Maven 에 추가 {#toc1}

일반적으로 Maven 를 사용해서 jar 파일들을 추가 하고 있다는 가정하에 진행합니다.
pom.xml 파일에 Maven Dependency 추가

```xml

<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
</dependency>



```

### Servlet 설정에 resource 설정 부분 확인 {#toc2}

```md
* 기본적으로 이클립스에서 Dynamic Web Project 로 프로젝트를 만들고 나면 
* web.xml 이라고하는 DD(Deployment Descriptor) 를 만들어줍니다.
* 해당 web.xml 에 servlet 설정 파일명 위치를 일반적으로 적어 놓는데요.. 
* 이름은 다를 수 있습니다. xxx-context.xml 이라고 보통 명시합니다.
Ex) web.xml 내용 일부
```

```xml
<servlet>
...
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/appServlet/servlet-context.xml
    </param-value>
...
</servlet>
```

Ex) servlet-context.xml 내용 일부

```xml
<beans ...>
...
    <mvc:resources mapping="/resources/**" location="/resources/" />
...
</beans>
```

```md
* 위에 처럼 resources mapping 을 정의 하는 부분이 Spring STS 를 사용해서 프로젝트를 만들거나
* 기타 사이트 예제에 기본적으로 명시되어 있는 경우가 일반적입니다.
* 내용의 의미는 /resources/** 말대로 /resources/js, /resources/css 와 같은 경로로 명시하는 경우에 
* /resources/ 경로로 해석하겠다 라는 의미로 보시면 됩니다.
* 문제는 처음 보시는 분들은 /resources/ 경로가 어디인지 모른다는게 문제겠지요..
* 이 경로는 Eclipse 프로젝스 구조상으로 보시면, src/main/webapp/resources 경로를 의미합니다. 
* resources 폴더가 보이지 않는다면 생성하시면 됩니다. 정의는 했지만 만들지 않았던것 뿐입니다.
```

### JSP 에 실제로 JS, CSS 불러보기 {#toc3}

test.jsp

```jsp
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html lang="en">
<head>
    <link href="<c:url value="/resources/css/main.css" />" rel="stylesheet">
    <script src="<c:url value="/resources/js/common.js" />"></script>
</head>
<body>
    Hello World
</body>
</html>

```

common.js

```js
alert("common");
```

### CSS 에서 img 파일을 읽어들이고 싶다면?? {#toc4}

예를 들어, 아래의 css 파일 이름이 test.css 이고, 해당 파일의 경로가 src/main/webapp/resources/css 폴더 안에 존재하고, img 파일은 src/main/webapp/resources/img 폴더 안에
존재한다면, 아래와 같이 경로를 입력하시면, 정상적으로 이미지 파일이 로드 될 것입니다.

```css

body {
 background-image: url("../img/paper.gif");
 background-color: #cccccc;
}

```

### Spring Boot ViewResolver Configuration {#toc5}

- 스프링부트 환경에서는 내장톰캣에 기본적으로 JSP 엔진을 포함하지 않고 있다.
- 맨위의 "JSTL 라이브러리 Maven 에 추가" 내용을 pom.xml 안에 추가하면 됨
- 그리고 아래 둘중 하나의 설정을 추가하면 됨

- application.properties 에 suffix 와 경로 추가

```yaml
spring.mvc.view.prefix=/WEB-INF/view/
spring.mvc.view.suffix=.js
```

- InternalResourceViewResolver 세팅

```java
@Configuration
@EnableWebMvc
@ComponentScan
public class MvcConfiguration extends WebMvcConfigurerAdapter
{
    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix("/WEB-INF/view/");
        resolver.setSuffix(".jsp");
        resolver.setViewClass(JstlView.class);
        registry.viewResolver(resolver);
    }
}

// main 에서 @SpringBootApplication 호출하면 
// 지정한 최상위 package 이하의 @ComponentScan 이 들어간 부분은 전부 읽음
// MvcConfiguration Bean 이 인식되어 설정이 적용 될 것임. 
// package 구조만 고려해서 MvcConfiguration.java 만들어 놓으면 됨
```

### 기타 관련 정보 {#toc6}

해당 test.jsp 로 화면을 뿌려주는 controller 샘플입니다.
viewresolver 설정은 되어 있는 것으로 가정합니다. 모르지면 spring viewresolver 로 검색하시면 관련 내용이 많이 나옵니다.
viewresolver 의 요점은 url 경로를 어떻게 해석해서 test.jsp 와 같은 파일을 호출 시키면서 data 를 전송하는가에 대한 내용으로 보입니다.

RootController.java

```java
@controller
@RequestMapping("/")
public class RootController {
    @RequestMapping(value = "/", method = {RequestMethod.GET, RequestMethod.POST})
    public String getMain() {
        return "test";
    }
}
```

```md
* RequestMapping 은 url 을 나타내고 / 는 기본 ROOT 를 말하는 것입니다.
* 아래에 getMain 에서도 / 기본 ROOT 경로로 정의 하고 내용을 정의 한 것입니다.
* return "test" 를 하고 끝인데, 이렇게 String 으로 던져버리면 viewresolver 는 test.jsp를 찾게 됩니다.
* 일반적으로 context 설정에서 viewresolver suffix 를 .jsp 로 설정하기 때문입니다.
* 요즘 스프링 부트는 기본적으로 jsp 를 지원하지 않는 추세로 알고 있습니다.
```

```md
로컬에서 톰캣으로 구동 하였다면, 톰캣에 있는 server.xml 내용에서
Context 설정 부분을 찾아보시면 path 정보가 적혀있습니다.
Ex) <Context docBase="springTest" path="/springTest" reloadable="true" source="org.eclipse.jst.jee.server:springTest"/>
path 에 /springTest 라고 적혀있는데 이것이 뜻하는 바는, localhost:8080/springTest 를 의미합니다. 해당 url로 시도해 보시면
test.jsp 내용이 나오게 됩니다.
```


