---
title: "Apache Ant Command Example"
description: "Basic Apache Ant Command"
categories: [ant]
tags: [ant]
redirect_from:
  - /2018/12/19/
---

> In this page, I will show you some of basic ant command that might be useful for application compile and deployment.

# Assumption & Background

Apache Ant should be installed and evironment path is set correctly.

# Ant Build Files

Let's create a file and name it as "build.xml"


```xml 

<?xml version = "1.0" encoding="utf-8"?>
<project name = "Hello World Project" default = "compile" basedir="/var/lib/jenkins/workspace/src">
   <target name = "info">
      <echo>Hello World - Welcome to Apache Ant!</echo>
   </target>
</project>

```

# Define Properties within the build.xml file

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


# Now add a new target (compileClass) that compiles java files.
This target has a task called "javac" which will compile java source code into class file.
I added "classpath" attribute within javac task to define classpath during compilation process.
To add external jar files, I used fileset attribute to define where those jar files are located.

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

# Add another target (deleteClass) which deletes class files. 
This target has a task called "delete" which is quite straight forward.
It will delete files within the "dir" path

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

# Add another target (deleteWeb) which deletes static files
Very similar with deleteClass task, except I specified types of file that I want to delete within the target directory "dir".

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

# Add another target (copyWeb) which copies static files
This target has a task called "copy" which will copy files into destination folder "todir"
You can use fileset attribute to specify types of file to copy from "dir"

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

# In case someon wish to use bash command within ant file, take a look at this sample (compileEJB)
I used exec task to run bash command. To run find command, I used dir and inputstring.
This sample maybe to simple. It would be a lot easier if you can run the bash script.
This is just a demo to show that it is possible to run bash command within ant script.
I also added and task to run another ant file that is located in same folder. 
Make user you use "target" attribute to specify what target you wish to run for the given "antfile"

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

# Hope it was useful to someone else. Cheers

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io