---
toc: true
title: "Ant: Apache Ant Make Ear, Jar, War, config Manifest Example"
description: "Ant : Apache Ant Make Ear, Jar, War, config Manifest Example"
categories: [Ant]
tags: [Ant]
redirect_from:
  - /2019/08/07/
---

> Apache Ant Make Ear, Jar, War, config Manifest Example

# Assumption & Background

Apache Ant should be installed and evironment path is set correctly.

### Apache Ant 사용 관련 {#toc1}
```md
보통 WAS 에 Application 자원을 배포하기 위해, 설정된 경로에 파일들을 복사해서 운영하는경우도 있고,
war, ear, jar 등등을 만들어서 사용하는 경우도 있습니다. 
ear 까지 만들어서 배포도 되게 자동화가 필요했고, runnable jar application 을 만들어서
java signing 까지 해야 하는 부분도 있어서 해당 부분들을 ant build.xml 파일에 작성해 보았습니다.

최근에는 이런방식을 잘 사용하지 않지요? 이유는 아마 dependancy library 들을 관리해주면서 하는
maven 이나 gradle 또는 annotation 으로 넘어가서 사용하게 되는 부분들로 인해
지금의 방식은 잘 사용하지 

물론 구글링해서 해당 소스를 참조해서 말이죠..
아래의 stackoverflow 의 답글에 작성한 build.xml 내용이 맘에 들어 참고하였습니다.
https://stackoverflow.com/questions/15247751/eclipse-with-java-ee-ejb-ant-and-jenkins-hudson/16109977#16109977

자 그럼 제가 만든 build.xml 을 보도록 하겠습니다.
```

### Ear, War, Jar, Java Signing 샘플 소스 {#toc2}
```md
사실 해보니, Ear, War, Jar 만드는게 엄청난게 있는 건 아닌것 같습니다.
jar 로 묶는건데 설정을 xml 에 정의 해서 그걸 바탕으로 묶는것과 Static 자원(html,css,js,img 등등) 의 폴더 위치 정도를 마춰주는정도
만 잘 되면 되는듯 합니다. 
소스가 좀 길어서 설명을 미리 적습니다.
기본적으로 property 정보를 적어서 사용했고, ant 명령어 실행시 기본으로 동작하는 build task 에 
depends 를 ear 를 만드는 걸 적었고, 그 안에는 war 를 만들고, jar 를 만들고, init 이라는 초기 task 까지 거꾸로 올라가게끔 되어 있습니다.
그래서 실제로 동작하는 순서는 init -> create-web -> create-ejb -> create-ear 순입니다. 물론 내부적으로 step 이 더 있습니다만,
순서가 이렇다고 하면 어떻게 동작할지는 이해 되시리라 생각합니다.

init 에 runnable jar 로 사용할 소스를 jar 로 그냥 만들고 만들 때 manifest 를 정의 해서 java signing 에 필요한 정보도 추가해 놓습니다.
그렇게 만들어진 jar 를 java signing 을 하고, 만든 jar 를 다시 엎어 치도록 만들었습니다.

좀 특이한 점은, 자바 소스들을 하나의 프로젝트에 다 모아 놓고, ant 로 돌리면 전체 소스를 먼저 compile 한 다음,
해당 compile 된 class 에서 각 영역 별로 사용되는 부분을 copy 해다가 war, jar, ear 를 만들어서 완성합니다.

도움이 될만한 사용 방법들을 적어 놓으면
javac 할때 JDK 홈 경로 변경 가능한 부분 소스가 있습니다. 인코딩 변경 옵션부분도 있습니다.
윈도우 bat 파일을 실행하는 부분의 소스가 있습니다.
파일 및 폴더를 복사할때 필터링 하는 부분의 소스가 있습니다. 
```
```xml 
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<project basedir="." default="build" name="TEST_APP">
    <property environment="env"/>
    <property name="debuglevel" value="source,lines,vars"/>
    <property name="target" value="1.6"/>
    <property name="source" value="1.6"/>
	<property name="builddir" value="antBuild" />
	<property name="outputartifacts" value="antOut" />
	<property name="ear.name" value="APP" />
	<property name="app.name" value="logviewscreen" />
	<property name="root.src" value="src" />
	<property name="javac.path" value="C:/Program Files/Java/jdk1.6.0_24/bin/javac" />
	<property name="app.builddir" value="${outputartifacts}/app" />
	<property name="app.builddir.classes" value="${app.builddir}/classes"/>
	<property name="sign.dir" location="signing"/>
	<property name="signing" location="signing/Auto Signer_1.6.bat"/>
	
	<property name="ejb.name" value="EJB" />
	<property name="ejb.module" value="ejbModule" />
	<!--
	<path id="ejb.src">
	  	<pathelement location="src/com/test/app/util" />
		<pathelement location="src/com/test/bl/ejb" />
		<pathelement location="src/com/test/common/constants" />
	</path>
	-->
	<property name="ejb.builddir" value="${outputartifacts}/ejb" />
	<property name="ejb.builddir.classes" value="${ejb.builddir}/classes" />	
	
	<property name="web.name" value="WEB" />
	<property name="web.src" value="src/com/test/app/controller" />
	<!--
	<path id="web.ref">
	  	<pathelement location="src/com/test/app/util" />
		<pathelement location="src/com/test/bl/ejb" />
		<pathelement location="src/com/test/common/constants" />
	</path>
	-->	
	<property name="web.builddir" value="${outputartifacts}/web" />
	<property name="web.builddir.classes" value="${web.builddir}/WEB-INF/classes"/>	
	
	<target name="build" depends="create-ear">
	    <echo message="==========================="/>
	    <echo message="  EAR Build Start     "/>
	    <echo message="==========================="/>	
	</target>
	<target name="create-ear" depends="create-web,create-ejb">
	    <ear destfile="${outputartifacts}/${ear.name}.ear" appxml="META-INF/application.xml">
	        <fileset dir="${outputartifacts}" >
	        	<patternset id="ejb.class.pattern">
	        		<include name="**/*.jar"/>
	        		<include name="**/*.war"/>
	        		<exclude name="**/logviewscreen.jar"/>
	        	</patternset>
	        </fileset>
	    </ear>
	</target>	
	<target name="create-ejb" depends="init-ejb">
	    <echo message="==========================="/>
	    <echo message="  Make ${ejb.name}.jar     "/>
	    <echo message="==========================="/>					
	    <jar destfile="${outputartifacts}/${ejb.name}.jar" basedir="${ejb.builddir.classes}" includes="**/*"/>
	</target>
	<!-- 
	<target name="build-ejb" depends="init-ejb">
	    <echo message="==========================="/>
	    <echo message="  Build EJB    "/>
	    <echo message="==========================="/>			
	    <javac fork="yes" executable="C:/Program Files/Java/jdk1.6.0_24/bin/javac" encoding="UTF-8" debug="true" debuglevel="${debuglevel}" destdir="${ejb.builddir.classes}" includeantruntime="false" source="${source}" target="${target}">
	        <src refid="ejb.src"/>
	        <classpath>
	            <fileset dir="lib">
	                <include name="**/*.jar"/>
	            </fileset>                  
	        </classpath>
	    </javac>
	</target>
	-->
	<target name="init-ejb" depends="init">
	    <mkdir dir="${ejb.builddir}" /> 
	    <copy includeemptydirs="false" todir="${ejb.builddir.classes}">
	    	<fileset dir="${ejb.module}">
	    	</fileset>
	    </copy>
	    <copy includeemptydirs="false" todir="${ejb.builddir.classes}">
	        <fileset dir="${builddir}">
				<patternset id="ejb.class.pattern">
					<include name="**/com/test/app/util/**"/>
					<include name="**/com/test/bl/ejb/**"/>
					<include name="**/com/test/common/constants/**"/>
				</patternset>
	        </fileset>
	    </copy>		
	</target>
	<target name="init">
	    <echo message="==========================="/>
	    <echo message="  Clear Build Folder (build,out) "/>
	    <echo message="==========================="/>
		<delete dir="${builddir}"/>
		<delete dir="${outputartifacts}"/>
	    <echo message="==========================="/>
	    <echo message="  Create Folders(build/[app,ejb,web],out) and Compile ALL JAVA FILES "/>
	    <echo message="==========================="/>
		<mkdir dir="${builddir}" />
	    <mkdir dir="${outputartifacts}" />
		<mkdir dir="${app.builddir.classes}" />
		<mkdir dir="${ejb.builddir.classes}" />
		<mkdir dir="${web.builddir.classes}" />
	    <javac fork="yes" executable="${javac.path}" encoding="UTF-8" debug="true" debuglevel="${debuglevel}" destdir="${builddir}" includeantruntime="false" source="${source}" target="${target}">
	        <src path="${root.src}"/>
	        <classpath>
	            <fileset dir="lib">
	                <include name="**/*.jar"/>
	            </fileset>  
	        </classpath>
	    </javac>
	    <copy includeemptydirs="false" todir="${app.builddir.classes}">
	        <fileset dir="${root.src}">
	        	<include name="**/com/test/ui/javaapp/img/**"/>
	        	<include name="**/com/test/ui/plaf/img/**"/>
	        	<include name="**/com/test/ui/plaf/resources/**"/>
	        	<exclude name="**/.dm/**"/>
	        </fileset>
	    </copy>		
	    <copy includeemptydirs="false" todir="${app.builddir.classes}">
	        <fileset dir="${builddir}">
				<patternset id="app.class.pattern">
					<include name="**/com/test/app/util/**"/>
					<include name="**/com/test/bl/ejb/**"/>
					<include name="**/com/test/common/constants/**"/>
					<include name="**/com/test/ui/**"/>
					<include name="**/com/test/**"/>
					<exclude name="**/.dm/**"/>
					<exclude name="**/test/**"/>
					<exclude name="**/controller/**"/>
					<exclude name="**/ejb/impl/**"/>
					<exclude name="**/PosConfigHandleException.class"/>
					<exclude name="**/PosConfigHandler.class"/>
					<exclude name="**/PosLogExtractor.class"/>
					<exclude name="**/PosLogHandler.class"/>					
				</patternset>
	        </fileset>
	    </copy>	    	
		<jar destfile="${outputartifacts}/${app.name}.jar" basedir="${app.builddir.classes}" includes="**/*">
			<manifest>
				<attribute name="Trusted-Library" value="true"/>
	            <attribute name="Application-Library-Allowable-Codebase" value="*"/>
	            <attribute name="Trusted-Only" value="true"/>				
	            <attribute name="Permissions" value="all-permissions"/>
				<attribute name="Caller-Allowable-Codebase" value="*"/>
	            <attribute name="Codebase" value="*"/>
	          <!-- details 
	          <section name="common/MyClass.class">
	            <attribute name="Sealed" value="false"/>
	          </section>
	          -->
			</manifest>
		</jar>
	    <copy includeemptydirs="false" todir="${sign.dir}">
	        <fileset dir="${outputartifacts}">
	        	<include name="**/logviewscreen.jar"/>
	        </fileset>
	    </copy>	    			
		<exec executable="cmd">
		    <arg value="/c"/>
		    <arg value="${signing}"/>
		</exec>
	    <copy includeemptydirs="false" todir="${outputartifacts}">
	        <fileset dir="${sign.dir}">
	        	<include name="**/logviewscreen.jar"/>
	        </fileset>
	    </copy>	    					
	</target>
	<target name="create-web" depends="init-web">
	    <echo message="==========================="/>
	    <echo message="  Make ${web.name}.war     "/>
	    <echo message="==========================="/>							
	    <war destfile="${outputartifacts}/${web.name}.war" basedir="${web.builddir}" webxml="WebContent/WEB-INF/web.xml"/>
	</target>
	<!-- 
	<target name="build-web" depends="init-web" >
	    <echo message="==========================="/>
	    <echo message="  BUILD WEB     "/>
	    <echo message="==========================="/>							
	    <javac fork="yes" executable="C:/Program Files/Java/jdk1.6.0_24/bin/javac" encoding="UTF-8" debug="true" debuglevel="${debuglevel}" destdir="${web.builddir.classes}" includeantruntime="false" source="${source}" target="${target}">
	        <src refid="web.ref"/>
	        <classpath>
	            <fileset dir="lib">
	                <include name="**/*.jar"/>
	            </fileset>  
	        </classpath>
	    </javac>
	</target>
	-->	
	<target name="init-web" depends="init">
	    <mkdir dir="${web.builddir.classes}"/>  
	    <copy includeemptydirs="false" todir="${web.builddir}">
	        <fileset dir="WebContent">
	        	<exclude name="**/.dm/**"/>
	        </fileset>
	    </copy>
	    <copy includeemptydirs="false" todir="${web.builddir.classes}">
	        <fileset dir="${builddir}">
				<patternset id="web.class.pattern">
					<include name="**/controller/**"/>
					<exclude name="**/.dm/**"/>
				</patternset>
	        </fileset>
	    </copy>
	</target>	
</project>

```

# Hope it was useful to someone else. Cheers

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io