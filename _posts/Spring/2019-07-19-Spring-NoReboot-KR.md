---
toc: true
title: "Spring Boot Build 없이 Run. 톰캣 서버 재기동 없이 수정 반영 class resource jsp 등등"
description: "Spring Boot Build 없이 Run. 톰캣 서버 재기동 없이 수정 반영 class resource jsp 등등"
categories: [Spring]
tags: [Spring]
redirect_from:
  - /2019/07/19/
---

> Spring Boot Build 없이 Run. 톰캣 서버 재기동 없이 수정 반영 class resource jsp 등등

### Spring Boot Build 없이 Run {#toc1}

```md
1. boot 서비스 pom.xml 파일에 아래 추가

<dependency>
 <groupId>org.springframework</groupId>
 <artifactId>springloaded</artifactId>
 <version>1.2.8.RELEASE</version>
</dependency>

2. run Configurations 설정
boot 서비스 우클릭 → Run As → Run Configurations → Spring Boot App  → Arguments → VM arguments 에디터 창에서 아래 설정 작성
-javaagent:C:\Users\Administrator\.m2\repository\org\springframework\springloaded\1.2.8.RELEASE\springloaded-1.2.8.RELEASE.jar -noverify
단, 경로는 본인 PC 경로 설정 할것

3. Mvn update 실시

4.  jar 파일 확인
C:\Users\Administrator\.m2\repository\org\springframework\springloaded\1.2.8.RELEASE\ 내에 springloaded-1.2.8.RELEASE.jar 확인

5. Run As → Spring Boot App
```

### Tomcat 서버에 설정하는 방법 {#toc2}

```bash
# 1번 내용에서 Maven Repository 로 경로를 잡든
# 그냥 Maven Central 에서 직접 jar 파일을 다운받든 어디든 내려 받은 다음
# 파일이 있는 경로에 CATALINA_OPTS 로 export 걸면, 톰캣 기동시 해당 옵션이 포함되어 서버가 기동 된다.
export CATALINA_OPTS="$CATALINA_OPTS -javaagent:/root/username/tomcat8.5/springloaded-1.2.8.RELEASE.jar -noverify"

# 위의 옵션을 적는 파일은 톰캣 폴더 구조에서 bin 폴더 안의 setenv.sh 파일을 생성해서 적는다
# 톰캣 8 에서는 이 파일이 존재하지 않는다. 생성하면 된다.
# 아래는 catalina.sh 파일의 내용 중 일부 인데, CATALINA_BASE 또는 CATALINA_HOME 으로 잡힌 경로 안에서 bin/setenv.sh 파일이 존재하면 해당 파일을 실행해 준다.
# 기본적으로 startup.sh 든 shutdown.sh 든 내부적으로 catalina.sh 를 호출하게 되어 있다. Ex) catalina.sh start or catalina.sh stop
 if [ -r "$CATALINA_BASE/bin/setenv.sh" ]; then
   . "$CATALINA_BASE/bin/setenv.sh"
 elif [ -r "$CATALINA_HOME/bin/setenv.sh" ]; then
   . "$CATALINA_HOME/bin/setenv.sh"
 fi
```

### Eclipse Tomcat 서버 설정 {#toc3}

```md
* 아래의 스샷들을 참고
* Server Option 관련 영역은 모두 체크 해제
* Publishing 관련 해서는 automatically publish when resourses change 선택
* server.xml 파일 안의 reloadable 속성 false로. 아래 정보 참고
<Context docBase="front" path="/" reloadable="false" source="org.eclipse.jst.jee.server:front"/>
* 마지막으로 아래 옵션을 넣어줘야 하는데.. 서버 만든거 더블클릭하면 뜨는 아래의 스샷 에서 Open launch configuration 클릭후 argument 탭 안의 VM argument 안에 아래의 명령어 입력. -D 옵션을 빼고 있는 그대로 붙여주면 됨. 경로는 알아서 다운받은 경로로 바꿔주기
-javaagent:C:\springloaded-1.2.8.RELEASE.jar -noverify 
```

![eclipse톰캣](/assets/images/screen/eclipse-tomcat01.png){: align-center}
서버 설정 화면

![eclipse톰캣](/assets/images/screen/eclipse-tomcat02.png){: align-center}
Open launch configuration 팝업 argument 세팅

### 하나의 톰캣 서버에 여려개의 Application 을 설정해서 원하는 대로 기동하는 방법 {#toc4}

```md
* 대부분 톰캣 서버 압축푼 폴더 전체를 복사해서 서버 별로 기동하는게 속이 편하긴 하다
* 하지만 난 궁금해서 한번 한대의 서버에 어떻게 하면 되는지 검색해 봤다
* 폴더 구조를 대충 그리자면
tomcat
 - bin (기존 모든 파일 존재)
 - controller (start.sh stop.sh 파일이 존재)
 - lib
app1
  - bin (여기 폴더에는 setenv.sh 파일만 존재)
  - conf
  - lib
  - logs
  - temp
  - webapps
  - work
app2
  - bin (여기 폴더에는 setenv.sh 파일만 존재)
  - conf
  - lib
  - logs
  - temp
  - webapps
  - work

* 포인트는 app1, app2 의 CATALINA_HOME, CATALINA_BASE 의 경로를 다르게 잡아주기 위해 파라미터를 받아서 
  app1, app2 경로로 바꿔주고, 기존 톰캣에 들어있던 startup.sh 파일을 호출
* 각 app1, app2 폴더안의 setenv.sh 파일에 CATALINA_OPTS 를 설정해서 기본적인 추가 옵션 처리

# start.sh stop.sh 파일 내용
#! /usr/bin/env sh

app_instance=$1;

BASE_TOMCAT=/home/yourBaseDirectory

export CATALINA_HOME=$BASE_TOMCAT/tomcat8.5
export CATALINA_BASE=$BASE_TOMCAT/$app_instance

$CATALINA_HOME/bin/startup.sh
# stop.sh 는 startup.sh 를 shutdown.sh 로 변경 하면 나머진 동일
$CATALINA_HOME/bin/shutdown.sh

# setenv.sh 파일 내용
export CATALINA_OPTS="$CATALINA_OPTS -Xms1024m"
export CATALINA_OPTS="$CATALINA_OPTS -Xmx4096m"
export CATALINA_OPTS="$CATALINA_OPTS -XX:MaxPermSize=1024m"
```

### Mybatis mapper 재기동 없이 인식 하는 방법 {#toc5}

아래 java 소스를 RefreshableSqlSessionFactoryBean.java 로 생성해서 원하는 package 경로에 넣고, 아래의 xml 파일 중, SqlSessionFactoryBean 을 찾아서 아래의 내용으로 교체

```xml
<!-- <bean id="sqlSession" class="org.mybatis.spring.SqlSessionFactoryBean"> -->
 <bean id="sqlSession" class="yeep.common.aop.RefreshableSqlSessionFactoryBean">
```

```java
package com.package.name;

import java.io.IOException;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Timer;
import java.util.TimerTask;
import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.DisposableBean;
import org.springframework.core.io.Resource;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class RefreshableSqlSessionFactoryBean extends SqlSessionFactoryBean implements DisposableBean {
 private static final Logger logger = LoggerFactory.getLogger(RefreshableSqlSessionFactoryBean.class);
 private SqlSessionFactory proxy;
 private int interval = 500;
 private Timer timer;
 private TimerTask task;
 private Resource[] mapperLocations;
 private boolean running = false;
 private final ReentrantReadWriteLock rwl = new ReentrantReadWriteLock();
 private final Lock r = rwl.readLock();
 private final Lock w = rwl.writeLock();

 public void setMapperLocations(Resource[] mapperLocations) {
  super.setMapperLocations(mapperLocations);
  this.mapperLocations = mapperLocations;
 }

 public void setInterval(int interval) {
  this.interval = interval;
 }

 public void refresh() throws Exception {
  if (logger.isInfoEnabled()) {
   logger.info("> Refresh SqlMapper");
   logger.info("======================================================================================");
  }
  w.lock();
  try {
   super.afterPropertiesSet();
  } finally {
   w.unlock();
  }
 }

 public void afterPropertiesSet() throws Exception {
  super.afterPropertiesSet();
  setRefreshable();
 }

 private void setRefreshable() {
  proxy = (SqlSessionFactory) Proxy.newProxyInstance(SqlSessionFactory.class.getClassLoader(),
    new Class[] { SqlSessionFactory.class }, new InvocationHandler() {
     public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
// log.debug("method.getName() : " + method.getName());
      return method.invoke(getParentObject(), args);
     }
    });
  task = new TimerTask() {
   private Map<Resource, Long> map = new HashMap<Resource, Long>();

   public void run() {
    if (isModified()) {
     try {
      refresh();
     } catch (Exception e) {
      logger.error("caught exception", e);
     }
    }
   }

   private boolean isModified() {
    boolean retVal = false;
    if (mapperLocations != null) {
     for (int i = 0; i < mapperLocations.length; i++) {
      Resource mappingLocation = mapperLocations[i];
      retVal |= findModifiedResource(mappingLocation);
     }
    }
    return retVal;
   }

   private boolean findModifiedResource(Resource resource) {
    boolean retVal = false;
    List<String> modifiedResources = new ArrayList<String>();
    try {
     long modified = resource.lastModified();
     if (map.containsKey(resource)) {
      long lastModified = ((Long) map.get(resource)).longValue();
      if (lastModified != modified) {
       map.put(resource, new Long(modified));
//modifiedResources.add(resource.getDescription()); // 전체경로
       modifiedResources.add(resource.getFilename()); // 파일명
       retVal = true;
      }
     } else {
      map.put(resource, new Long(modified));
     }
    } catch (IOException e) {
     logger.error("caught exception", e);
    }
    if (retVal) {
     if (logger.isInfoEnabled()) {
      logger.info(
        "======================================================================================");
      logger.info("> Update File name : " + modifiedResources);
     }
    }
    return retVal;
   }
  };
  timer = new Timer(true);
  resetInterval();
 }

 private Object getParentObject() throws Exception {
  r.lock();
  try {
   return super.getObject();
  } finally {
   r.unlock();
  }
 }

 public SqlSessionFactory getObject() {
  return this.proxy;
 }

 public Class<? extends SqlSessionFactory> getObjectType() {
  return (this.proxy != null ? this.proxy.getClass() : SqlSessionFactory.class);
 }

 public boolean isSingleton() {
  return true;
 }

 public void setCheckInterval(int ms) {
  interval = ms;
  if (timer != null) {
   resetInterval();
  }
 }

 private void resetInterval() {
  if (running) {
   timer.cancel();
   running = false;
  }
  if (interval > 0) {
   timer.schedule(task, 0, interval);
   running = true;
  }
 }

 public void destroy() throws Exception {
  timer.cancel();
 }
}
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
