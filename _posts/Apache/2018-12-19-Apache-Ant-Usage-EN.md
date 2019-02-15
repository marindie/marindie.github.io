---
toc: true
title: "아파치 (Apache) ANT 명령어 예제"
description: "기본적인 Apache ANT 명령어 설명"
categories: [ant]
tags: [ant]
redirect_from:
  - /2018/12/19/
---

> 기본적인 Apache ANT 명령어와 관련된 샘플 예제를 다루고 있습니다.

# 배경지식

아파치 (Apache) Ant 가 설치 되어 있고 환경 변수 세팅등의 설정이 되어 있다는 가정하에 진행합니다.

# Ant Build Files

ANT 의 가장 기본적인 실행 명령은 "ant" 입니다. 이때 내부적으로 ANT 는 "build.xml" 를 대상으로 하는 파일을 찾고, project 에 default 로 되어 있는 값을 찾아서
그 값을 target 이름으로 하는 내용을 수행하게 됩니다.


```xml 

<?xml version = "1.0" encoding="utf-8"?>
<project name = "Hello World Project" default = "compile" basedir="/var/lib/jenkins/workspace/src">
   <target name = "info">
      <echo>Hello World - Welcome to Apache Ant!</echo>
   </target>
</project>

```

# Property 항목 정의 하기 (build.xml file 내부에 정의)

property 정보를 build.properties 파일로 빼서 정의 하셔도 되고, build.xml 내부에 정의 하셔도 됩니다. 

```xml

<?xml version = "1.0" encoding="utf-8"?>
<project name = "Hello World Project" default = "compile" basedir="/var/lib/jenkins/workspace/src">
        <property environment="env"/>
        <property name="src.dir" value="${basedir}" />
        <property name="webcom.dir" value="/was/upwas/WebApp/WEBCOMMON/lib" />
        <property name="common.dir" value="/was/upwas/WebApp/COMMON/lib" />
        <property name="portal.dir" value="/was/upwas/WebApp/MyApplication" />
        <property name="classes.dir" value="/was/upwas/WebApp/MyApplication/WEB-INF/classes" />
        <property name="lib.dir" value="/was/upwas/WebApp/MyApplication/WEB-INF/lib" />
        <property name="ejb.dir" value="/was/upwas/src/ejb/MyApplication" />
        <property name="weblogic.module.dir" value="/was/weblogic1036/modules" />
        <property name="jenkins.ejb.dir" value="/var/lib/jenkins/workspace/MyApplication/src/com/nate/webadmin/ejb" />
        <property name="jenkins.web.dir" value="/var/lib/jenkins/workspace/MyApplication/WebContent" />
        <property name="web.dir" value="/web/apache2.2.31/htdocs/MyApplication" />
   <target name = "info">
      <echo>Hello World - Welcome to Apache Ant!</echo>
   </target>
</project>

```

# Property 항목 정의 하기 (build.properties file 내부에 정의후 build.xml 내에서 include)

```xml

<?xml version = "1.0" encoding="utf-8"?>
<project name = "Hello World Project" default = "compile" basedir="/var/lib/jenkins/workspace/src">
        <property file="build.properties"/>
   <target name = "info">
      <echo>Hello World - Welcome to Apache Ant!</echo>
   </target>
</project>

```

# target 추가 하기 (compileClass)
자바 파일을 컴파일하는 "javac" task 를 정의 하고, target 명을 compileClass 라고 명시하여 정의 해보겠습니다.
"classpath" 속성을 사용하여 컴파일 진행시 대상으로 삼을 java 소스 위치의 최상단 폴더명을 적어 놓습니다. 
그 안의 폴더가 존재하여도 알아서 포함하여 컴파일 합니다.
외부 라이브러리를 (external jar files) 포함 하기 위해서, fileset 속성을 사용하여 위치 범위를 정의 하였습니다.
해당 폴더 내에 jar 형태의 파일들을 모두 포함하겠다는 의미 입니다.

```xml

<?xml version = "1.0" encoding="utf-8"?>
<project name = "Hello World Project" default = "compile" basedir="/var/lib/jenkins/workspace/src">
	<property environment="env"/>
	<property name="src.dir" value="${basedir}" />
	...

	<target name = "info">
		<echo>Hello World - Welcome to Apache Ant!</echo>
	</target>

    <target name="compileClass" >
        <echo message="==========================="/>
        <echo message="  Compile Java Files Start "/>
        <echo message="==========================="/>		
        <javac srcdir="${src.dir}" destdir="${classes.dir}" encoding="euc-kr">
            <include name="**/*.java"/>
			<exclude name="**/*Bean.java"/>
            <classpath>
                <pathelement path="${classes.dir}" />
                <fileset dir="${lib.dir}">
                        <include name="**/*.jar"/>
                </fileset>
                <fileset dir="${webcom.dir}">
                        <include name="**/*.jar"/>
                </fileset>
                <fileset dir="${weblogic.module.dir}">
                        <include name="**/*.jar"/>
                </fileset>
        	</classpath>
        </javac>
        <echo message="==========================="/>
        <echo message="  Compile Java Files End   "/>
        <echo message="==========================="/>				
    </target>

</project>

```

# target 추가 하기 (deleteClass) 
이름에서 알 수 있듯, Static(CSS, JS, HTML) 파일과 같이 정적 파일이나, 기존에 컴파일된 class 파일들을 삭제하고 
컴파일 하기 위해서 사용하였습니다.

```xml

<?xml version = "1.0" encoding="utf-8"?>
<project name = "Hello World Project" default = "compile" basedir="/var/lib/jenkins/workspace/src">
	<property environment="env"/>
	<property name="src.dir" value="${basedir}" />
	...

	<target name = "info">
		<echo>Hello World - Welcome to Apache Ant!</echo>
	</target>

    <target name="deleteClass" >
        <echo message="==========================="/>
        <echo message="  Delete Class Files       "/>
        <echo message="==========================="/>
        <delete dir="${classes.dir}/com" />
    </target>	

    <target name="compileClass" >
        <echo message="==========================="/>
        <echo message="  Compile Java Files Start "/>
        <echo message="==========================="/>		
        <javac srcdir="${src.dir}" destdir="${classes.dir}" encoding="euc-kr">
            <include name="**/*.java"/>
			<exclude name="**/*Bean.java"/>
            <classpath>
                <pathelement path="${classes.dir}" />
                <fileset dir="${lib.dir}">
                        <include name="**/*.jar"/>
                </fileset>
                <fileset dir="${webcom.dir}">
                        <include name="**/*.jar"/>
                </fileset>
                <fileset dir="${weblogic.module.dir}">
                        <include name="**/*.jar"/>
                </fileset>
        	</classpath>
        </javac>
        <echo message="==========================="/>
        <echo message="  Compile Java Files End   "/>
        <echo message="==========================="/>				
    </target>

</project>

```

# target 추가 하기 (deleteWeb)
위의 내용과 같은 목적이며 처리 되는 방식에 있어서 좀더 상세하게 정의 하여 처리되도록 하였습니다.
원하는 파일, 원치 않는 파일을 include, exclude 를 사용하여 정의 할 수 있고, 폴더 level 로 정의도 가능합니다.

```xml

<?xml version = "1.0" encoding="utf-8"?>
<project name = "Hello World Project" default = "compile" basedir="/var/lib/jenkins/workspace/src">
	<property environment="env"/>
	<property name="src.dir" value="${basedir}" />
	...

	<target name = "info">
		<echo>Hello World - Welcome to Apache Ant!</echo>
	</target>

    <target name="deleteWeb" >
        <echo message="============================="/>
        <echo message="  Delete Static Files Start  "/>
        <echo message="============================="/>
        <delete>
            <fileset dir="${web.dir}">
                <include name="**/*.html"/>
                <include name="**/css/**"/>
                <include name="**/guide/**"/>
                <include name="**/images/**"/>
                <include name="**/js/**"/>
                <include name="**/robots.txt"/>
            </fileset>
        </delete>
        <echo message="============================="/>
        <echo message="  Delete Static Files End    "/>
        <echo message="============================="/>
    </target>	

    <target name="deleteClass" >
        <echo message="==========================="/>
        <echo message="  Delete Class Files       "/>
        <echo message="==========================="/>
        <delete dir="${classes.dir}/com" />
    </target>	

    <target name="compileClass" >
        <echo message="==========================="/>
        <echo message="  Compile Java Files Start "/>
        <echo message="==========================="/>		
        <javac srcdir="${src.dir}" destdir="${classes.dir}" encoding="euc-kr">
            <include name="**/*.java"/>
			<exclude name="**/*Bean.java"/>
            <classpath>
                <pathelement path="${classes.dir}" />
                <fileset dir="${lib.dir}">
                        <include name="**/*.jar"/>
                </fileset>
                <fileset dir="${webcom.dir}">
                        <include name="**/*.jar"/>
                </fileset>
                <fileset dir="${weblogic.module.dir}">
                        <include name="**/*.jar"/>
                </fileset>
        	</classpath>
        </javac>
        <echo message="==========================="/>
        <echo message="  Compile Java Files End   "/>
        <echo message="==========================="/>				
    </target>

</project>

```

# target 추가 하기 (copyWeb)
"copy" task 를 사용하여 원하는 대상 폴더에 복사를 하는 작업을 정의해 보았습니다. "todir"
fileset 속성을 사용하여 원하는 종류의 파일들만 선택하여 복사도 가능합니다.

```xml

<?xml version = "1.0" encoding="utf-8"?>
<project name = "Hello World Project" default = "compile" basedir="/var/lib/jenkins/workspace/src">
	<property environment="env"/>
	<property name="src.dir" value="${basedir}" />
	...

	<target name = "info">
		<echo>Hello World - Welcome to Apache Ant!</echo>
	</target>

    <target name="deleteWeb" >
        <echo message="============================="/>
        <echo message="  Delete Static Files Start  "/>
        <echo message="============================="/>
        <delete>
            <fileset dir="${web.dir}">
                <include name="**/*.html"/>
                <include name="**/css/**"/>
                <include name="**/guide/**"/>
                <include name="**/images/**"/>
                <include name="**/js/**"/>
                <include name="**/robots.txt"/>
            </fileset>
        </delete>
        <echo message="============================="/>
        <echo message="  Delete Static Files End    "/>
        <echo message="============================="/>
    </target>	

    <target name="copyWeb" >
        <echo message="==========================="/>
        <echo message="  Copy Static Files Start  "/>
        <echo message="==========================="/>
        <copy todir="${web.dir}">
            <fileset dir="${jenkins.web.dir}">
                <include name="**/*.html"/>
                <include name="**/css/**"/>
                <include name="**/guide/**"/>
                <include name="**/images/**"/>
                <include name="**/js/**"/>
                <include name="**/robots.txt"/>
                <exclude name="**/.svn/**"/>
                <exclude name="**/jsp/**"/>
            </fileset>
        </copy>
        <echo message="==========================="/>
        <echo message="  Copy Static Files End    "/>
        <echo message="==========================="/>
    </target>    

    <target name="deleteClass" >
        <echo message="==========================="/>
        <echo message="  Delete Class Files       "/>
        <echo message="==========================="/>
        <delete dir="${classes.dir}/com" />
    </target>	

    <target name="compileClass" >
        <echo message="==========================="/>
        <echo message="  Compile Java Files Start "/>
        <echo message="==========================="/>		
        <javac srcdir="${src.dir}" destdir="${classes.dir}" encoding="euc-kr">
            <include name="**/*.java"/>
			<exclude name="**/*Bean.java"/>
            <classpath>
                <pathelement path="${classes.dir}" />
                <fileset dir="${lib.dir}">
                        <include name="**/*.jar"/>
                </fileset>
                <fileset dir="${webcom.dir}">
                        <include name="**/*.jar"/>
                </fileset>
                <fileset dir="${weblogic.module.dir}">
                        <include name="**/*.jar"/>
                </fileset>
        	</classpath>
        </javac>
        <echo message="==========================="/>
        <echo message="  Compile Java Files End   "/>
        <echo message="==========================="/>				
    </target>

</project>

```

# 번외 적인 Tip. 쉘 명령어 (bash command) 를 ant 에서 사용을 해보고 싶어서 가능한지 한번 시도해 보았습니다. (compileEJB)
단순히 count 를 하는 simple 한 케이스 입니다. 이 하나의 명령어를 처리하기 위해, 결과값을 받기 위한 세팅 
명령어 세팅 등 어느정도 코딩하는 듯한 느낌이 들게끔 되어 있습니다.

```xml

<?xml version = "1.0" encoding="utf-8"?>
<project name = "Hello World Project" default = "compile" basedir="/var/lib/jenkins/workspace/src">
	<property environment="env"/>
	<property name="src.dir" value="${basedir}" />
	...

	<target name = "info">
		<echo>Hello World - Welcome to Apache Ant!</echo>
	</target>
	...

    <target name="compileEJB">
        <exec executable="bash" dir="${ejb.dir}" inputstring="find -type f -name *.java | wc -l" outputproperty="ejb.java.count"/>
        <echo message="==========================="/>
        <echo message=" EBJ File Counts = ${ejb.java.count} "/>
        <echo message="==========================="/>

        <echo message="==========================="/>
        <echo message="  TestBean Start  "/>
        <echo message="==========================="/>
        <ant antfile="Test_build.xml" target="all"/>
        <echo message="==========================="/>
        <echo message="  TestBean End  "/>
        <echo message="==========================="/>
	</target>	

</project>

```

# ANT 사용에 도움이 되었으면 합니다. 감사합니다.

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io