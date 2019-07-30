---
toc: true
title: "Java: Java RMI, EJB JNDI 사용법 정리"
description: "Java RMI, EJB JNDI 사용법 정리"
categories: [Java]
tags: [Java]
redirect_from:
  - /2019/07/30/
---

> Java RMI, EJB JNDI 사용법 정리

### JAVA RMI {#toc1}
```md
JAVA 에서 제공하는 RMI 사용방식으로 매우 간단하고, WAS(Middleware) 에 종속적이지 않다.
무슨말인가 하면, WAS 에서는 WAS 내부에 REMOTE 설정이 가능하고, 
weblogic은 t3://IP:PORT, jboss는 remote://IP:PORT 를 사용한다. http:// 가 아니라는말.
JAVA RMI 도 다른데 rmi://IP:PORT 이다.
JAVA RMI 의 특징으로는 rmiregistry 를 기동시켜서 설정한 IP, PORT 로 서버를 기동해야 하는 불편함이 있다.
아래는 JAVA RMI SAMPLE.
```
Interface
```java
import java.rmi.Remote;
import java.rmi.RemoteException;
import java.util.Date;

public interface Test extends Remote 
{
    String testLog(String keyword, String term, String logPath) throws RemoteException;
}
```
Implement
main 함수가 rmiregistry 를 실행하는 것과 같은 역할을 수행한다.
```java
import java.rmi.RemoteException;
import java.rmi.registry.LocateRegistry;
import java.rmi.registry.Registry;
import java.rmi.server.UnicastRemoteObject;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;

public class TestImpl implements Test{
	
    public TestImpl() throws RemoteException {
		super();
		// TODO Auto-generated constructor stub
	}

	public String testLog(String keyword, String term, String logPath) 
    {
    	int iTerm = 0;
        try 
        {
            System.out.println("REMOTE METHOD INVOKED");
        } catch (Exception e) 
        {
            // do nothing...
        }
        return "GOOD";
    }

    //rmi://127.0.0.1:1099/TESTAPP 으로 호출 하면 TestImpl 객체를 return 해줘, 해당 객체의 method를 사용할 수 있게 해줌.
    public static void main(String[] args){
        try {
        	System.setProperty("java.rmi.server.hostname", "127.0.0.1");
        	System.out.println("java.rmi.server.hostname = "+System.getProperty("java.rmi.server.hostname"));
        	Test app = new TestImpl();
        	Test appStub = (Test)UnicastRemoteObject.exportObject(app, 0);

            Registry registry = LocateRegistry.createRegistry( 1099 );
            registry = LocateRegistry.getRegistry();
            registry.rebind("TESTAPP", appStub);        	
            System.out.println("RMI Server Ready. URL = //127.0.0.1:1099/TESTAPP");
        } catch (Exception e) {
            System.err.println("Server exception: " + e.toString());
            e.printStackTrace();
        }
    }    
```
Client 쪽에서 사용시
```java
import java.rmi.Naming;

Test test = (TESTEAPP) Naming.lookup("rmi://127.0.0.1:1099/TESTAPP");
System.out.println(test.testLog("asdfas","asdfasd","asdf"));
```
### JBoss EJB JNDI(RMI) 사용법 {#toc2}
```md
RMI 를 기록하는데 왜 갑자기 EJB JNDI 가 나오나 싶을것 같다. 
EJB 내부에 RMI 기능이 내장되어 있는것으로 알고 있다. 

EJB 를 만들고, 위의 JAVA RMI 처럼 객체를 받기 위해서는, JNDI Context lookup 을 사용하여 해당 객체를 서버에서 받는방식으로 사용했다.

나도 잘 아는 부분이 아니라서 그냥 경험을 바탕으로 기록해 둔다.
.ear 파일 안에 
XXXEJB.jar 파일인 EJB 및 관련 class를 묶은 jar 파일과 
XXXWEB.war 파일인 Web Application 관련 파일들을 묶은 war 파일이 들어간다.

.ear 파일 안에 META-INF 폴더안에 application.xml 을 작성하는데 아래는 샘플
<?xml version = '1.0' encoding = 'UTF-8'?>
<!DOCTYPE application PUBLIC "-//Sun Microsystems, Inc.//DTD J2EE Application 1.3//EN" "http://java.sun.com/dtd/application_1_3.dtd">
<application>
   <display-name>TESTAPP</display-name>
   <module>
      <ejb>TESTEJB.jar</ejb>
   </module>
   <module>
      <web>
         <web-uri>TESTWEB.war</web-uri>
         <context-root>/TEST</context-root>
      </web>
   </module>
   <module>
      <ejb>TESTEJB2.jar</ejb>
   </module>
   <module>
      <web>
         <web-uri>TESTWEB2.war</web-uri>
         <context-root>/TEST2</context-root>
      </web>
   </module>
</application>
폴더 구조
TESTAPP.ear 
    - TESTEJB.jar
    - TESTWEB.war
    - TESTEJB2.jar
    - TESTWEB2.war
    - META-INF(폴더)
        - application.xml
        - MANIFEST.MF (여기엔 내용이 별게 없었다. 아래 두줄이 전부)
            Manifest-Version: 1.0
            Created-By: 1.6.0_35 (Sun Microsystems Inc.)

이제부터는 TESTEJB.jar 와 TESTWEB.war 의 자원에 EJB-JNDI 를 설정해서 사용하는 법을 설명한다.
Middleware 인 Weblogic과 Jboss 는 Bender 사 별로 INITIAL_CONTEXT_FACTORY 를 지정하여 사용한다.
```
JBoss Client 에서 EJB 호출시 사용하는 소스
```java
        Context ctx = null;
        Hashtable env = new Hashtable();
        env.put(Context.PROVIDER_URL, rmiInfo.get(PosAnalyzerConstants.URL));       
        env.put(Context.INITIAL_CONTEXT_FACTORY, "org.jboss.naming.remote.client.InitialContextFactory"); //jboss 사용시
        env.put(Context.URL_PKG_PREFIXES, "org.jboss.ejb.client.naming");
        env.put("jboss.naming.client.ejb.context", "true");
        env.put("jboss.naming.client.connect.options.org.xnio.Options.SASL_POLICY_NOPLAINTEXT", "false");
       	env.put(Context.SECURITY_PRINCIPAL, "user");
       	env.put(Context.SECURITY_CREDENTIALS, "password");   
        ctx = new InitialContext(env);
        TestHome home = (TestHome) ctx.lookup("TESTAPP/TESTEJB/Test!com.test.TestHome");
        Test test = (Test)home.create();
        test.runMethod();
```
```md
정신건강을 위해 JBoss6.4 를 로컬에 설치해 놓고 테스트 하기 바란다. 
JAVA_HOME 환경변수가 잡혀있어야 한다. 
standalone.bat 로 실행시켜서 테스트 성공한 케이스이다. 
zip 으로 받은 Jboss6.4 에서 bin/standalone.bat 를 실행했고
standalone/configuration/standalone.xml 에서 아래 내용이 remote 부분에 대한 설정으로 알고 있다.
상세 설정이 있는것 같지만, 대충 짐작으로는 <remote> 가 remote 연결을 한다는 의미의 설정으로,
<socket-binding> 이 어떤 PORT 로 remote 를 받을건지 설정하는 것으로 이해했다.
<remote connector-ref="remoting-connector" thread-pool-name="default"/>
<socket-binding name="remoting" port="4647"/>
remote://127.0.0.1:4647 이 JBoss 의 remote URL 이 된다.

Client 에서 EJB JNDI 를 사용하기 위해서는 library 가 필요한데, JBoss6.4 에 bin/client 폴더가 있다
거기에 jboss-cli-client.jar, jboss-client.jar 를 classpath 에 등록해주면 된다. 적어도 내가 만든 소스에서는 되었다..

EJB Session Bean 을 ejb-jar.xml 파일에 정의 해두고,
TESTEJB.jar 의 META-INF 폴더에 넣는다.
<?xml version = '1.0' encoding = 'EUC-KR'?>
<!DOCTYPE ejb-jar PUBLIC "-//Sun Microsystems, Inc.//DTD Enterprise JavaBeans 2.0//EN" "http://java.sun.com/dtd/ejb-jar_2_0.dtd">
<ejb-jar>
  <enterprise-beans>
    <session>
      <description>Session Bean ( Stateless )</description>
      <display-name>Test</display-name>
      <ejb-name>Test</ejb-name>
      <home>com.test.TestHome</home>
      <remote>com.test.Test</remote>
      <ejb-class>com.test.TestBean</ejb-class>
      <session-type>Stateless</session-type>
      <transaction-type>Container</transaction-type>
    </session>
  </enterprise-beans>
</ejb-jar>

같은 META-INF 폴더 안에 jboss-ejb-client.xml 파일을 만들고
아래의 내용을 기술한다.
<jboss-ejb-client xmlns="urn:jboss:ejb-client:1.0">
   <client-context>
     <ejb-receivers>
        <remoting-ejb-receiver outbound-connection-ref="remote-ejb-connection"/>
     </ejb-receivers>
   </client-context>
 </jboss-ejb-client>

 TESTWEB.war 안에 META-INF 폴더 안에 web.xml 을 만들고 설정을 한다.
 Servlet 3.0 이상부터는 보통 Annotation 기반의 sample 소스가 많은듯 하다. 이거는 옜날 동작방식에서
 못벗어 나는 상황에서 수정이 발생할 때, 이해를 하기 위해 적어 놓았다.
 <?xml version = '1.0' encoding = 'EUC-KR'?>
<!DOCTYPE web-app PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN" "http://java.sun.com/dtd/web-app_2_3.dtd">
<web-app>
  <description>Empty web.xml file for Web Application</description>
  <servlet>
    <servlet-name>TestController</servlet-name>
    <servlet-class>com.test.TestController</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>TestController</servlet-name>
    <url-pattern>/testurl</url-pattern>
  </servlet-mapping>
  <session-config>
    <session-timeout>30</session-timeout>
  </session-config>
  <mime-mapping>
    <extension>html</extension>
    <mime-type>text/html</mime-type>
  </mime-mapping>
  <mime-mapping>
    <extension>txt</extension>
    <mime-type>text/plain</mime-type>
  </mime-mapping>
  <mime-mapping>
    <extension>jnlp</extension>
    <mime-type>application/x-java-jnlp-file</mime-type>
  </mime-mapping>
  <welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>index.html</welcome-file>
  </welcome-file-list>
</web-app>

해당 자원들의 설정 정보가 정상적으로 들어가 있는 상태에서
TESTAPP.ear 를 JBoss6.4 의 standalone/deployments 안에 복사해 두고
bin/standalone.bat 를 실행하면 서버 기동이 되는데, 정상적으로 떳다면,
아래와 같은 로그가 보여야 한다.

10:39:25,780 INFO  [org.jboss.as.ejb3.deployment.processors.EjbJndiBindingsDeploymentUnitProcessor] (MSC service thread 1-8) JNDI bindings for session bean named Test in deployment unit su
bdeployment "TESTEJB.jar" of deployment "TESTEAPP.ear" are as follows:

        java:global/TESTEAPP/TESTEJB/Test!com.test.TestHome
        java:app/TESTEJB/Test!com.test.TestHome
        java:module/Test!com.test.TestHome
        java:jboss/exported/TESTEAPP/TESTEJB/Test!com.test.TestHome
        java:global/TESTEAPP/TESTEJB/Test!com.test.Test
        java:app/TESTEJB/Test!com.test.Test
        java:module/Test!com.test.Test
        java:jboss/exported/TESTEAPP/TESTEJB/Test!com.test.Test

이런 로그가 나온다는 것을 JNDI Binding 이 되었다는 의미로, ejb-jar.xml 에 기술한 내용이 등록되었다는 의미이다.        
```
```java
// 위에 아래의 샘플 소스가 있었는데, lookup 부분을 보면 
// java:jboss/exported/TESTEAPP/TESTEJB/Test!com.test.TestHome 와 비슷함을 알 수 있다.
// 잘은 모르겠지만, 지금 내가 설정한 Context 의 상태에서는 TESTEAPP/TESTEJB/Test!com.test.TestHome 를 넣으면
// 정상적으로 해당 객체를 리턴해 준다.
// 테스트 서버에서 테스트시, ID, PWD 관련 에러가 나는데, 설정의 문제이지, RMI 에러와 관련된 부분은 아닌듯하다.
// Remote 로 접속할때 사용하는 인증 방식 설정이 없거나.. 잘못 되어 있는것이 아닐가.. 생각해본다..
// 로컬에서 잘 되는 이유는 Local auth 동작방식으로 동작하기 때문에, 사용자 계정정보와 관계없이 bypass 되는듯해서 인듯 하다.
// 참고로 사용자를 admin 과 application user 2명 이 필요한데, bin/add-user.bat 로 a 는 admin b 는 application user 를 등록할 수 있다.
// 만드려고 하다보면 어려운점은 없는데, group 할당을 할 거냐고 물어 본다. 안하면 default 그룹을 만들고 이 이름이 괜찮냐고 물어 본다. yes 하면 됩.
TestHome home = (TestHome) ctx.lookup("TESTAPP/TESTEJB/Test!com.test.TestHome");        
```

### Weblogic EJB JNDI(RMI) 사용법 {#toc3}
```md
JBoss에 설명한 내용의 대부분이 그대로 사용된다. 
다른 부분은 사용할때 필요로 하는 library 와 Context.INITIAL_CONTEXT_FACTORY 와 같은 Context 설정 정보 내용이 
weblogic 용으로 마춰져야 한다. 사실 기존에 이미 되는 소스가 있어서, 정확히 어떤 설정들이 되어야 하는지
로컬 테스트를 못하고 그냥 일단 기록해 둔다.
다행히, weblogic EJB JNDI 는 인터넷에 좀 샘플이 괜찮게 있는편이라고 생각한다.

아래의 library 들이 기존에 있었다. 사실 어디까지가 EJB JNDI 의 범주에 해당하는지를 몰라 일단 다 적어놓는다.
wlthint3client.jar
jndi.jar
javax.ejb_3.0.1.jar
javax.servlet_1.0.0.0_2-5.jar
jmxri.jar
```

Welogic용 Client가 EJB JNDI 사용시 사용하는 소스
```java
// 왜 TESTAPP 으로 이름이 따지는가 생각해 보면, 기본적으로 .ear 파일의 이름을 따서 만드는 듯 하다.
// 이 부분은 변경이 가능할 것 같고, 정확하지 않으니 믿지 말것. 그냥 추측일 뿐. 로그에 정보가 나오는 지도 확인
// 안해본 상태의 정보임.
        Context ctx = null;
        Hashtable env = new Hashtable();
        env.put(Context.PROVIDER_URL, rmiInfo.get(PosAnalyzerConstants.URL));       
        env.put(Context.INITIAL_CONTEXT_FACTORY, "weblogic.jndi.WLInitialContextFactory"); //weblogic 사용시
       	env.put(Context.SECURITY_PRINCIPAL, "user");
       	env.put(Context.SECURITY_CREDENTIALS, "password");   
        ctx = new InitialContext(env);
        TestHome home = (TestHome) PortableRemoteObject.narrow(ctx.lookup("TESTAPP"), "com.test.TestHome");
        Test test = (Test)home.create();
        test.runMethod();
```
```md
마지막으로 JBoss 의 기본 REMOTE PORT 는 4447, Weblogic 은 9403 인듯하다.
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
