---
toc: true
title: "Spring: Eureka, Zuul, 그리고 Swagger 을 사용하여 API Gateway, Eureka Server, Eureka Discovery(Eureka Service), API Documentation 구성하기"
description: "Netflix Eureka, Zuul, 그리고 Swagger 을 사용하여 API Gateway, Eureka Server, Eureka Discovery(Eureka Service), API Documentation 을 구성하는 방법에 대한 간략한 요약입니다."
categories: [Spring]
tags: [Eureka, Zuul, Swagger]
redirect_from:
  - /2019/01/10/
---

> Netflix Eureka, Zuul, 그리고 Swagger 을 사용하여 API Gateway, Eureka Server, Eureka Discovery(Eureka Service), API Documentation 을 구성하는 방법에 대한 간략한 요약입니다.

# https://start.spring.io/ 사용하여 Project 구성 및 Eclipse 에 Import 하기

저는 이 구성을 위해 2개의 서버 프로젝트를 생성 하였습니다.

첫번째 프로젝트: 
https://start.spring.io/ 접속.
Groujp Artifact 는 취향대로 적고, (Group: com.test.main, Artifact: eureka-zuul-server)
Dependencies 에 web, Eureka Server, Zuul 을 검색하여 추가 하고 Generate Project 클릭

두번째 프로젝트: 
https://start.spring.io/ 접속.
Groujp Artifact 는 취향대로 적고, (Group: com.test.main, Artifact: eureka-discovery-server)
Dependencies 에 web, Eureka Discovery 을 검색하여 추가 하고 Generate Project 클릭

Eclipse 에 프로젝트 Import 하기
File => Import => Existing Maven Projects 클릭 후 Next
Root Directory 에 각 샘플 소스 압축한 경로 지정후 Finish
인터넷이 연결되어 있고 자동 빌드가 세팅되어 있으면, pom.xml 에 정의된 내용을 확인하여 필요한 jar 파일들을 Maven Dependencies 항목에 추가 하기 위해 Download 를 시도한다.

# Eureka Server 설정

프로젝트 생성시, 자동으로 EurekaZuulServerApplication.java 라는 이름으로 파일을 생성해주는데,
아래는 EurekaZuulServerApplication.java 파일 내용.
SpringBootApplication 어노테이션만 기본으로 들어가 있는데,
EnableZuulProxy 와 EnableEurekaServer 를 추가해 준다. 추가 하기 위해 import 가 필요한것은 당연. Maven Update 에서 뭔가 잘못되서 Import 안되었을 수도 있으니 확인.

```java

package com.wony.main.eurekazuulserver;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@EnableZuulProxy
@EnableEurekaServer
@SpringBootApplication
public class EurekaZuulServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(EurekaZuulServerApplication.class, args);
	}

}

```

그 다음에 src/main/resources 안에  application.yml 파일을 생성하여 아래의 내용으로 작성
아래는 application.yml

```yml

spring:
  application:
    name: eureka-service

server:
  port: 8071
    
eureka:
  client:
    registerWithEureka: false
    fetchRegistry: false
    server:
      waitTimeInMsWhenSyncEmpty: 0    
      
zuul:
  prefix: /api
  routes:
    master-api:
      path: /**
      url: http://localhost:8070    

```

대략적인 내용은 application name 을 명시하고, 서버 PORT 를 8071 로 잡고, eureka 설정을 false 로 세팅하여 server 라는점을 정의 하고
zuul 세팅에 http://localhost:8071/api/ 로 들어오는 요청을 http://localhost:8070 으로 넘기겠다는 Proxy 설정을 하는 것이다.

내용을 저장 후, Project 클릭 후, 마우스 우클릭 => Run As => Maven Install 하면, 실행가능한 jar 파일이 해당 프로젝트 경로의 target 폴더 안에 만들어 진다.
java -jar xxxxx.jar 를 실행시키면 tomcat 내장되어 있어서 해당 localhost:8071 로 기동됨.

이해에 도움이 되었으면 합니다. 감사합니다.

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io