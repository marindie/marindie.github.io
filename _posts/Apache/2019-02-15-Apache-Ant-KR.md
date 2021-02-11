---
toc: true
title: "Apache Ant 명령어 Example"
description: "Basic Apache Ant 명령어"
categories: [Script]
tags: [Ant]
redirect_from:
  - /2019/02/15/
---

> In this page, I will show you some of basic ant command that might be useful for application compile and deployment.

### 배경지식 {#toc1}

```md
- Apache Ant 가 설치되어 있고 환경변수 관련 설정이 정상적으로 되어 있어야 합니다.
```

### Ant Build Files {#toc2}

```md
- build.xml 이란 파일을 하나 생성합니다.
```

```xml
<?xml version = "1.0" encoding="utf-8"?>
<project name = "Hello World Project" default = "compile" basedir="/var/lib/jenkins/workspace/src">
   <target name = "info">
      <echo>Hello World - Welcome to Apache Ant!</echo>
   </target>
</project>
```

### Build.xml 파일 안에 Properties 변수 정의 {#toc3}

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

### Java 파일을 컴파일하는 compileClass 이라는 target 작성  {#toc4}

```md
- javac 라는 task 를 사용하여 java source code 를 class file 로 컴파일 합니다.
- javac 안의 classpath 속성을 정의하여 컴파일에 사용할 classpath 위치를 잡습니다.
- 외부 jar 라이브러리를 추가 하기 위해, fileset 속성을 사용하여 jar file 위치를 잡아줍니다.
```

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

### deletes class files 을 삭제하는 deleteClass target 정의  {#toc5}

```md
- delete task 를 사용하여 dir path 안의 files 들을 삭제합니다.
- 삭제 관련 Regex 는 설명을 건너 띕니다.
```

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

### static files 들을 삭제하는 deleteWeb target 정의 {#toc6}

```md
- deleteClass task 와 거의 똑같습니다. 
- 추가적으로 원하는 파일 형태의 포맷을 정의하여 해당하는 파일만 삭제합니다.
```

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

### Add another target copyWeb which copies static files  {#toc7}

```md
- This target has a task called "copy" which will copy files into destination folder "todir"
You can use fileset attribute to specify types of file to copy from "dir"
```

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

### In case someon wish to use bash command within ant file take a look at this sample compileEJB {#toc8}

```md
- I used exec task to run bash command. To run find command, I used dir and inputstring.
- This sample maybe to simple. It would be a lot easier if you can run the bash script.
- This is just a demo to show that it is possible to run bash command within ant script.
- I also added and task to run another ant file that is located in same folder. 
- Make user you use "target" attribute to specify what target you wish to run for the given "antfile"
```

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

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io