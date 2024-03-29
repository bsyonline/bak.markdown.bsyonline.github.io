---
title: 编译hadoop-1.2.1 eclipse插件
toc: true
date: 2016-07-16 15:45:46
tags: hadoop
categories: technology
---


### 1. 准备工作
#### 1.1 安装ant
#### 1.2下载hadoop-1.2.1.tar.gz，解压缩到D:\\hadoop\\hadoop-1.2.1
### 2. 修改配置文件
#### 1） D:\\hadoop\\hadoop-1.2.1\\src\\contrib\\eclipse-plugin\\build.xml
	<?xml version="1.0" encoding="UTF-8" standalone="no"?>

	<!--
	   Licensed to the Apache Software Foundation (ASF) under one or more
	   contributor license agreements.  See the NOTICE file distributed with
	   this work for additional information regarding copyright ownership.
	   The ASF licenses this file to You under the Apache License, Version 2.0
	   (the "License"); you may not use this file except in compliance with
	   the License.  You may obtain a copy of the License at

	       http://www.apache.org/licenses/LICENSE-2.0

	   Unless required by applicable law or agreed to in writing, software
	   distributed under the License is distributed on an "AS IS" BASIS,
	   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
	   See the License for the specific language governing permissions and
	   limitations under the License.
	-->

	<project default="jar" name="eclipse-plugin">

	  <import file="../build-contrib.xml"/>

	  <path id="eclipse-sdk-jars">
	    <fileset dir="${eclipse.home}/plugins/">
	      <include name="org.eclipse.ui*.jar"/>
	      <include name="org.eclipse.jdt*.jar"/>
	      <include name="org.eclipse.core*.jar"/>
	      <include name="org.eclipse.equinox*.jar"/>
	      <include name="org.eclipse.debug*.jar"/>
	      <include name="org.eclipse.osgi*.jar"/>
	      <include name="org.eclipse.swt*.jar"/>
	      <include name="org.eclipse.jface*.jar"/>

	      <include name="org.eclipse.team.cvs.ssh2*.jar"/>
	      <include name="com.jcraft.jsch*.jar"/>
	    </fileset>
	  </path>

	  <!-- Override classpath to include Eclipse SDK jars -->
	  <path id="classpath">
	    <pathelement location="${build.classes}"/>
	    <pathelement location="${hadoop.root}/build/classes"/>
	    <path refid="eclipse-sdk-jars"/>
	     <fileset dir="${hadoop.root}/">
	      <include name="*.jar"/>
	</fileset>
	  </path>

	  <!-- Skip building if eclipse.home is unset. -->
	  <target name="check-contrib" unless="eclipse.home">
	    <property name="skip.contrib" value="yes"/>
	    <echo message="eclipse.home unset: skipping eclipse plugin"/>
	  </target>

	<target name="compile" depends="init, ivy-retrieve-common" unless="skip.contrib">
	    <echo message="contrib: ${name}"/>
	    <javac
	     encoding="${build.encoding}"
	     srcdir="${src.dir}"
	     includes="**/*.java"
	     destdir="${build.classes}"
	     debug="${javac.debug}"
	     deprecation="${javac.deprecation}">
	     <classpath refid="classpath"/>
	    </javac>
	  </target>

	  <!-- Override jar target to specify manifest -->
	  <target name="jar" depends="compile" unless="skip.contrib">
	    <mkdir dir="${build.dir}/lib"/>
	     <!--
	    <copy file="${hadoop.root}/build/hadoop-core-${version}.jar" tofile="${build.dir}/lib/hadoop-core.jar" verbose="true"/>
	    <copy file="${hadoop.root}/build/ivy/lib/Hadoop/common/commons-cli-${commons-cli.version}.jar"  todir="${build.dir}/lib" verbose="true"/>-->
	     <copy file="${hadoop.root}/hadoop-core-${version}.jar" tofile="${build.dir}/lib/hadoop-core.jar" verbose="true"/>
	    <copy file="${hadoop.root}/lib/commons-cli-1.2.jar"  todir="${build.dir}/lib" verbose="true"/>
	    <copy file="${hadoop.root}/lib/commons-lang-2.4.jar"  todir="${build.dir}/lib" verbose="true"/>
	    <copy file="${hadoop.root}/lib/commons-configuration-1.6.jar"  todir="${build.dir}/lib" verbose="true"/>
	    <copy file="${hadoop.root}/lib/jackson-mapper-asl-1.8.8.jar"  todir="${build.dir}/lib" verbose="true"/>
	    <copy file="${hadoop.root}/lib/jackson-core-asl-1.8.8.jar"  todir="${build.dir}/lib" verbose="true"/>
	    <copy file="${hadoop.root}/lib/commons-httpclient-3.0.1.jar"  todir="${build.dir}/lib" verbose="true"/>
	    <jar
	      jarfile="${build.dir}/hadoop-${name}-${version}.jar"
	      manifest="${root}/META-INF/MANIFEST.MF">
	      <fileset dir="${build.dir}" includes="classes/ lib/"/>
	      <fileset dir="${root}" includes="resources/ plugin.xml"/>
	    </jar>
	  </target>

	</project>
#### 2） D:\hadoop\hadoop-1.2.1\src\contrib\build-contrib.xml
	<?xml version="1.0"?>

	<!--
	   Licensed to the Apache Software Foundation (ASF) under one or more
	   contributor license agreements.  See the NOTICE file distributed with
	   this work for additional information regarding copyright ownership.
	   The ASF licenses this file to You under the Apache License, Version 2.0
	   (the "License"); you may not use this file except in compliance with
	   the License.  You may obtain a copy of the License at

	       http://www.apache.org/licenses/LICENSE-2.0

	   Unless required by applicable law or agreed to in writing, software
	   distributed under the License is distributed on an "AS IS" BASIS,
	   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
	   See the License for the specific language governing permissions and
	   limitations under the License.
	-->

	<!-- Imported by contrib/*/build.xml files to share generic targets. -->

	<project name="hadoopbuildcontrib" xmlns:ivy="antlib:org.apache.ivy.ant">

	  <property name="name" value="${ant.project.name}"/>
	  <property name="root" value="${basedir}"/>
	  <property name="version" value="1.2.1"/>
	  <property name="ivy.version" value="2.1.0"/>
	  <property name="eclipse.home" location="D:/Program Files/eclipse-jee-indigo-SR2-win32-x86_64"/>
	  <property name="hadoop.root" location="D:/hadoop/hadoop-1.2.1"/>

	  <!-- Load all the default properties, and any the user wants    -->
	  <!-- to contribute (without having to type -D or edit this file -->
	  <property file="${user.home}/${name}.build.properties" />
	  <property file="${root}/build.properties" />
	  <property file="${hadoop.root}/build.properties" />

	  <property name="src.dir"  location="${root}/src/java"/>
	  <property name="src.test" location="${root}/src/test"/>
	  <property name="src.test.data" location="${root}/src/test/data"/>
	  <!-- Property added for contrib system tests -->
	  <property name="build-fi.dir" location="${hadoop.root}/build-fi"/>
	  <property name="system-test-build-dir" location="${build-fi.dir}/system"/>
	  <property name="src.test.system" location="${root}/src/test/system"/>

	  <property name="src.examples" location="${root}/src/examples"/>

	  <available file="${src.examples}" type="dir" property="examples.available"/>
	  <available file="${src.test}" type="dir" property="test.available"/>

	  <!-- Property added for contrib system tests -->
	  <available file="${src.test.system}" type="dir"
	      property="test.system.available"/>

	  <property name="conf.dir" location="${hadoop.root}/conf"/>
	  <property name="test.junit.output.format" value="plain"/>
	  <property name="test.output" value="no"/>
	  <property name="test.timeout" value="900000"/>
	  <property name="build.contrib.dir" location="${hadoop.root}/build/contrib"/>
	  <property name="build.dir" location="${hadoop.root}/build/contrib/${name}"/>
	  <property name="build.classes" location="${build.dir}/classes"/>
	  <property name="build.test" location="${build.dir}/test"/>
	  <property name="build.examples" location="${build.dir}/examples"/>
	  <property name="hadoop.log.dir" location="${build.dir}/test/logs"/>
	  <!-- all jars together -->
	  <property name="javac.deprecation" value="off"/>
	  <property name="javac.debug" value="on"/>
	  <property name="build.ivy.lib.dir" value="${hadoop.root}/build/ivy/lib"/>

	  <property name="javadoc.link"
	            value="http://java.sun.com/j2se/1.4/docs/api/"/>

	  <property name="build.encoding" value="ISO-8859-1"/>

	  <fileset id="lib.jars" dir="${root}" includes="lib/*.jar"/>

	  <!-- Property added for contrib system tests -->
	  <property name="build.test.system" location="${build.dir}/system"/>
	  <property name="build.system.classes"
	      location="${build.test.system}/classes"/>

	   <!-- IVY properties set here -->
	  <property name="ivy.dir" location="ivy" />
	  <!-- loglevel take values like default|download-only|quiet -->
	  <property name="loglevel" value="quiet"/>
	  <property name="ivysettings.xml" location="${hadoop.root}/ivy/ivysettings.xml"/>
	  <loadproperties srcfile="${ivy.dir}/libraries.properties"/>
	  <loadproperties srcfile="${hadoop.root}/ivy/libraries.properties"/>
	  <property name="ivy.jar" location="${hadoop.root}/ivy/ivy-${ivy.version}.jar"/>
	  <property name="ivy_repo_url"
	     value="http://repo2.maven.org/maven2/org/apache/ivy/ivy/${ivy.version}/ivy-${ivy.version}.jar" />
	  <property name="build.dir" location="build" />
	  <property name="build.ivy.dir" location="${build.dir}/ivy" />
	  <property name="build.ivy.lib.dir" location="${build.ivy.dir}/lib" />
	  <property name="build.ivy.report.dir" location="${build.ivy.dir}/report" />
	  <property name="common.ivy.lib.dir" location="${build.ivy.lib.dir}/${ant.project.name}/common"/>

	  <!--this is the naming policy for artifacts we want pulled down-->
	  <property name="ivy.artifact.retrieve.pattern"
	                   value="${ant.project.name}/[conf]/[artifact]-[revision].[ext]"/>

	  <!-- the normal classpath -->
	  <path id="contrib-classpath">
	    <pathelement location="${build.classes}"/>
	    <pathelement location="${hadoop.root}/build/tools"/>
	    <fileset refid="lib.jars"/>
	    <pathelement location="${hadoop.root}/build/classes"/>
	    <fileset dir="${hadoop.root}/lib">
	      <include name="**/*.jar" />
	    </fileset>
	    <path refid="${ant.project.name}.common-classpath"/>
	    <pathelement path="${clover.jar}"/>
	  </path>

	  <!-- the unit test classpath -->
	  <path id="test.classpath">
	    <pathelement location="${build.test}" />
	    <pathelement location="${hadoop.root}/build/test/classes"/>
	    <pathelement location="${hadoop.root}/src/contrib/test"/>
	    <pathelement location="${conf.dir}"/>
	    <pathelement location="${hadoop.root}/build"/>
	    <pathelement location="${build.examples}"/>
	    <pathelement location="${hadoop.root}/build/examples"/>
	    <path refid="contrib-classpath"/>
	  </path>

	  <!-- The system test classpath -->
	  <path id="test.system.classpath">
	    <pathelement location="${hadoop.root}/src/contrib/${name}/src/test/system" />
	    <pathelement location="${build.test.system}" />
	    <pathelement location="${build.test.system}/classes"/>
	    <pathelement location="${build.examples}"/>
	    <pathelement location="${hadoop.root}/build-fi/system/classes" />
	    <pathelement location="${hadoop.root}/build-fi/system/test/classes" />
	    <pathelement location="${hadoop.root}/build-fi" />
	    <pathelement location="${hadoop.root}/build-fi/tools" />
	    <pathelement location="${hadoop.home}"/>
	    <pathelement location="${hadoop.conf.dir}"/>
	    <pathelement location="${hadoop.conf.dir.deployed}"/>
	    <pathelement location="${hadoop.root}/build"/>
	    <pathelement location="${hadoop.root}/build/examples"/>
	    <pathelement location="${hadoop.root}/build-fi/test/classes" />
	    <path refid="contrib-classpath"/>
	    <fileset dir="${hadoop.root}/src/test/lib">
	      <include name="**/*.jar" />
	      <exclude name="**/excluded/" />
	    </fileset>
	    <fileset dir="${hadoop.root}/build-fi/system">
	       <include name="**/*.jar" />
	       <exclude name="**/excluded/" />
	     </fileset>
	    <fileset dir="${hadoop.root}/build-fi/test/testjar">
	      <include name="**/*.jar" />
	      <exclude name="**/excluded/" />
	    </fileset>
	    <fileset dir="${hadoop.root}/build/contrib/${name}">
	      <include name="**/*.jar" />
	      <exclude name="**/excluded/" />
	    </fileset>
	  </path>

	  <!-- to be overridden by sub-projects -->
	  <target name="check-contrib"/>
	  <target name="init-contrib"/>

	  <!-- ====================================================== -->
	  <!-- Stuff needed by all targets                            -->
	  <!-- ====================================================== -->
	  <target name="init" depends="check-contrib" unless="skip.contrib">
	    <echo message="contrib: ${name}"/>
	    <mkdir dir="${build.dir}"/>
	    <mkdir dir="${build.classes}"/>
	    <mkdir dir="${build.test}"/>
	    <!-- The below two tags  added for contrib system tests -->
	    <mkdir dir="${build.test.system}"/>
	    <mkdir dir="${build.system.classes}"/>
	    <mkdir dir="${build.examples}"/>
	    <mkdir dir="${hadoop.log.dir}"/>
	    <antcall target="init-contrib"/>
	  </target>


	  <!-- ====================================================== -->
	  <!-- Compile a Hadoop contrib's files                       -->
	  <!-- ====================================================== -->
	  <target name="compile" depends="init, ivy-retrieve-common" unless="skip.contrib">
	    <echo message="contrib: ${name}"/>
	    <javac
	     encoding="${build.encoding}"
	     srcdir="${src.dir}"
	     includes="**/*.java"
	     destdir="${build.classes}"
	     debug="${javac.debug}"
	     deprecation="${javac.deprecation}">
	     <classpath refid="contrib-classpath"/>
	    </javac>
	  </target>


	  <!-- ======================================================= -->
	  <!-- Compile a Hadoop contrib's example files (if available) -->
	  <!-- ======================================================= -->
	  <target name="compile-examples" depends="compile" if="examples.available">
	    <echo message="contrib: ${name}"/>
	    <javac
	     encoding="${build.encoding}"
	     srcdir="${src.examples}"
	     includes="**/*.java"
	     destdir="${build.examples}"
	     debug="${javac.debug}">
	     <classpath refid="contrib-classpath"/>
	    </javac>
	  </target>


	  <!-- ================================================================== -->
	  <!-- Compile test code                                                  -->
	  <!-- ================================================================== -->
	  <target name="compile-test" depends="compile-examples" if="test.available">
	    <echo message="contrib: ${name}"/>
	    <javac
	     encoding="${build.encoding}"
	     srcdir="${src.test}"
	     includes="**/*.java"
	     excludes="system/**/*.java"
	     destdir="${build.test}"
	     debug="${javac.debug}">
	    <classpath refid="test.classpath"/>
	    </javac>
	  </target>

	  <!-- ================================================================== -->
	  <!-- Compile system test code                                           -->
	  <!-- ================================================================== -->
	  <target name="compile-test-system" depends="compile-examples"
	     if="test.system.available">
	    <echo message="contrib: ${name}"/>
	    <javac
	       encoding="${build.encoding}"
	       srcdir="${src.test.system}"
	       includes="**/*.java"
	       destdir="${build.system.classes}"
	       debug="${javac.debug}">
	      <classpath refid="test.system.classpath"/>
	    </javac>
	  </target>

	  <!-- ====================================================== -->
	  <!-- Make a Hadoop contrib's jar                            -->
	  <!-- ====================================================== -->
	  <target name="jar" depends="compile" unless="skip.contrib">
	    <echo message="contrib: ${name}"/>
	    <jar
	      jarfile="${build.dir}/hadoop-${name}-${version}.jar"
	      basedir="${build.classes}"     
	    />
	  </target>


	  <!-- ====================================================== -->
	  <!-- Make a Hadoop contrib's examples jar                   -->
	  <!-- ====================================================== -->
	  <target name="jar-examples" depends="compile-examples"
	          if="examples.available" unless="skip.contrib">
	    <echo message="contrib: ${name}"/>
	    <jar jarfile="${build.dir}/hadoop-${name}-examples-${version}.jar">
	      <fileset dir="${build.classes}">
	      </fileset>
	      <fileset dir="${build.examples}">
	      </fileset>
	    </jar>
	  </target>

	  <!-- ====================================================== -->
	  <!-- Package a Hadoop contrib                               -->
	  <!-- ====================================================== -->
	  <target name="package" depends="jar, jar-examples" unless="skip.contrib">
	    <mkdir dir="${dist.dir}/contrib/${name}"/>
	    <copy todir="${dist.dir}/contrib/${name}" includeEmptyDirs="false" flatten="true">
	      <fileset dir="${build.dir}">
	        <include name="hadoop-${name}-${version}.jar" />
	      </fileset>
	    </copy>
	  </target>

	  <!-- ================================================================== -->
	  <!-- Run unit tests                                                     -->
	  <!-- ================================================================== -->
	  <target name="test" depends="compile-test, compile" if="test.available">
	    <echo message="contrib: ${name}"/>
	    <delete dir="${hadoop.log.dir}"/>
	    <mkdir dir="${hadoop.log.dir}"/>
	    <junit
	      printsummary="yes" showoutput="${test.output}"
	      haltonfailure="no" fork="yes" maxmemory="512m"
	      errorProperty="tests.failed" failureProperty="tests.failed"
	      timeout="${test.timeout}">

	      <sysproperty key="test.build.data" value="${build.test}/data"/>
	      <sysproperty key="build.test" value="${build.test}"/>
	      <sysproperty key="src.test.data" value="${src.test.data}"/>
	      <sysproperty key="contrib.name" value="${name}"/>

	      <!-- requires fork=yes for:
	        relative File paths to use the specified user.dir
	        classpath to use build/contrib/*.jar
	      -->
	      <sysproperty key="user.dir" value="${build.test}/data"/>

	      <sysproperty key="fs.default.name" value="${fs.default.name}"/>
	      <sysproperty key="hadoop.test.localoutputfile" value="${hadoop.test.localoutputfile}"/>
	      <sysproperty key="hadoop.log.dir" value="${hadoop.log.dir}"/>
	      <sysproperty key="taskcontroller-path" value="${taskcontroller-path}"/>
	      <sysproperty key="taskcontroller-ugi" value="${taskcontroller-ugi}"/>
	      <classpath refid="test.classpath"/>
	      <formatter type="${test.junit.output.format}" />
	      <batchtest todir="${build.test}" unless="testcase">
	        <fileset dir="${src.test}"
	                 includes="**/Test*.java" excludes="**/${test.exclude}.java, system/**/*.java" />
	      </batchtest>
	      <batchtest todir="${build.test}" if="testcase">
	        <fileset dir="${src.test}" includes="**/${testcase}.java" excludes="system/**/*.java" />
	      </batchtest>
	    </junit>
	    <antcall target="checkfailure"/>
	  </target>

	  <!-- ================================================================== -->
	  <!-- Run system tests                                                   -->
	  <!-- ================================================================== -->
	  <target name="test-system" depends="compile, compile-test-system, jar"
	     if="test.system.available">
	     <delete dir="${build.test.system}/extraconf"/>
	     <mkdir dir="${build.test.system}/extraconf"/>
	     <property name="test.src.dir" location="${hadoop.root}/src/test"/>
	     <property name="test.junit.printsummary" value="yes" />
	     <property name="test.junit.haltonfailure" value="no" />
	     <property name="test.junit.maxmemory" value="512m" />
	     <property name="test.junit.fork.mode" value="perTest" />
	     <property name="test.all.tests.file" value="${test.src.dir}/all-tests" />
	     <property name="test.build.dir" value="${hadoop.root}/build/test"/>
	     <property name="basedir" value="${hadoop.root}"/>
	     <property name="test.timeout" value="900000"/>
	     <property name="test.junit.output.format" value="plain"/>
	     <property name="test.tools.input.dir" value="${basedir}/src/test/tools/data"/>
	     <property name="c++.src" value="${basedir}/src/c++"/>
	     <property name="test.include" value="Test*"/>
	     <property name="c++.libhdfs.src" value="${c++.src}/libhdfs"/>
	     <property name="test.build.data" value="${build.test.system}/data"/>
	     <property name="test.cache.data" value="${build.test.system}/cache"/>
	     <property name="test.debug.data" value="${build.test.system}/debug"/>
	     <property name="test.log.dir" value="${build.test.system}/logs"/>
	     <patternset id="empty.exclude.list.id" />
	        <exec executable="sed" inputstring="${os.name}"
	            outputproperty="nonspace.os">
	          <arg value="s/ /_/g"/>
	        </exec>
	     <property name="build.platform"
	         value="${nonspace.os}-${os.arch}-${sun.arch.data.model}"/>
	     <property name="build.native"
	         value="${hadoop.root}/build/native/${build.platform}"/>
	     <property name="lib.dir" value="${hadoop.root}/lib"/>
	     <property name="install.c++.examples"
	         value="${hadoop.root}/build/c++-examples/${build.platform}"/>
	    <condition property="tests.testcase">
	       <and>
	         <isset property="testcase" />
	       </and>
	    </condition>
	     <property name="test.junit.jvmargs" value="-ea" />
	    <macro-system-test-runner test.file="${test.all.tests.file}"
	                     classpath="test.system.classpath"
	                     test.dir="${build.test.system}"
	                     fileset.dir="${hadoop.root}/src/contrib/${name}/src/test/system"
	                     hadoop.conf.dir.deployed="${hadoop.conf.dir.deployed}">
	  </macro-system-test-runner>
	  </target>

	  <macrodef name="macro-system-test-runner">
	    <attribute name="test.file" />
	    <attribute name="classpath" />
	    <attribute name="test.dir" />
	    <attribute name="fileset.dir" />
	    <attribute name="hadoop.conf.dir.deployed" default="" />
	    <sequential>
	      <delete dir="@{test.dir}/data"/>
	      <mkdir dir="@{test.dir}/data"/>
	      <delete dir="@{test.dir}/logs"/>
	      <mkdir dir="@{test.dir}/logs"/>
	      <copy file="${test.src.dir}/hadoop-policy.xml"
	        todir="@{test.dir}/extraconf" />
	      <copy file="${test.src.dir}/fi-site.xml"
	        todir="@{test.dir}/extraconf" />
	      <junit showoutput="${test.output}"
	        printsummary="${test.junit.printsummary}"
	        haltonfailure="${test.junit.haltonfailure}"
	        fork="yes"
	        forkmode="${test.junit.fork.mode}"
	        maxmemory="${test.junit.maxmemory}"
	        dir="${basedir}" timeout="${test.timeout}"
	        errorProperty="tests.failed" failureProperty="tests.failed">
	        <jvmarg value="${test.junit.jvmargs}" />
	        <sysproperty key="java.net.preferIPv4Stack" value="true"/>
	        <sysproperty key="test.build.data" value="@{test.dir}/data"/>
	        <sysproperty key="test.tools.input.dir" value = "${test.tools.input.dir}"/>
	        <sysproperty key="test.cache.data" value="${test.cache.data}"/>
	        <sysproperty key="test.debug.data" value="${test.debug.data}"/>
	        <sysproperty key="hadoop.log.dir" value="@{test.dir}/logs"/>
	        <sysproperty key="test.src.dir" value="@{fileset.dir}"/>
	        <sysproperty key="taskcontroller-path" value="${taskcontroller-path}"/>
	        <sysproperty key="taskcontroller-ugi" value="${taskcontroller-ugi}"/>
	        <sysproperty key="test.build.extraconf" value="@{test.dir}/extraconf" />
	        <sysproperty key="hadoop.policy.file" value="hadoop-policy.xml"/>
	        <sysproperty key="java.library.path"
	          value="${build.native}/lib:${lib.dir}/native/${build.platform}"/>
	        <sysproperty key="install.c++.examples" value="${install.c++.examples}"/>
	        <syspropertyset dynamic="no">
	          <propertyref name="hadoop.tmp.dir"/>
	        </syspropertyset>
	        <!-- set compile.c++ in the child jvm only if it is set -->
	        <syspropertyset dynamic="no">
	          <propertyref name="compile.c++"/>
	        </syspropertyset>
	        <!-- Pass probability specifications to the spawn JVM -->
	        <syspropertyset id="FaultProbabilityProperties">
	          <propertyref regex="fi.*"/>
	        </syspropertyset>
	        <sysproperty key="test.system.hdrc.deployed.hadoopconfdir"
	                     value="@{hadoop.conf.dir.deployed}" />
	        <classpath refid="@{classpath}"/>
	        <formatter type="${test.junit.output.format}" />
	        <batchtest todir="@{test.dir}" unless="testcase">
	          <fileset dir="@{fileset.dir}"
	            excludes="**/${test.exclude}.java aop/** system/**">
	            <patternset>
	              <includesfile name="@{test.file}"/>
	            </patternset>
	          </fileset>
	        </batchtest>
	        <batchtest todir="@{test.dir}" if="testcase">
	          <fileset dir="@{fileset.dir}" includes="**/${testcase}.java"/>
	        </batchtest>
	      </junit>
	      <antcall target="checkfailure"/>
	    </sequential>
	  </macrodef>


	  <target name="checkfailure" if="tests.failed">
	    <touch file="${build.contrib.dir}/testsfailed"/>
	    <fail unless="continueOnFailure">Contrib Tests failed!</fail>
	  </target>

	  <!-- ================================================================== -->
	  <!-- Clean.  Delete the build files, and their directories              -->
	  <!-- ================================================================== -->
	  <target name="clean">
	    <echo message="contrib: ${name}"/>
	    <delete dir="${build.dir}"/>
	  </target>

	  <target name="ivy-probe-antlib" >
	    <condition property="ivy.found">
	      <typefound uri="antlib:org.apache.ivy.ant" name="cleancache"/>
	    </condition>
	  </target>


	  <target name="ivy-download" description="To download ivy " unless="offline">
	    <get src="${ivy_repo_url}" dest="${ivy.jar}" usetimestamp="true"/>
	  </target>

	  <target name="ivy-init-antlib" depends="ivy-download,ivy-probe-antlib" unless="ivy.found">
	    <typedef uri="antlib:org.apache.ivy.ant" onerror="fail"
	      loaderRef="ivyLoader">
	      <classpath>
	        <pathelement location="${ivy.jar}"/>
	      </classpath>
	    </typedef>
	    <fail >
	      <condition >
	        <not>
	          <typefound uri="antlib:org.apache.ivy.ant" name="cleancache"/>
	        </not>
	      </condition>
	      You need Apache Ivy 2.0 or later from http://ant.apache.org/
	      It could not be loaded from ${ivy_repo_url}
	    </fail>
	  </target>

	  <target name="ivy-init" depends="ivy-init-antlib">
	    <ivy:configure settingsid="${ant.project.name}.ivy.settings" file="${ivysettings.xml}"/>
	  </target>

	  <target name="ivy-resolve-common" depends="ivy-init">
	    <ivy:resolve settingsRef="${ant.project.name}.ivy.settings" conf="common" log="${loglevel}"/>
	  </target>

	  <target name="ivy-retrieve-common" depends="ivy-resolve-common"
	    description="Retrieve Ivy-managed artifacts for the compile/test configurations">
	    <ivy:retrieve settingsRef="${ant.project.name}.ivy.settings"
	      pattern="${build.ivy.lib.dir}/${ivy.artifact.retrieve.pattern}" sync="true" log="${loglevel}"/>
	    <ivy:cachepath pathid="${ant.project.name}.common-classpath" conf="common" />
	  </target>
	</project>

#### 3） D:\hadoop\hadoop-1.2.1\src\contrib\eclipse-plugin\META-INF\MANIFEST.MF
	Manifest-Version: 1.0
	Bundle-ManifestVersion: 2
	Bundle-Name: MapReduce Tools for Eclipse
	Bundle-SymbolicName: org.apache.hadoop.eclipse;singleton:=true
	Bundle-Version: 0.18
	Bundle-Activator: org.apache.hadoop.eclipse.Activator
	Bundle-Localization: plugin
	Require-Bundle: org.eclipse.ui,
	org.eclipse.core.runtime,
	org.eclipse.jdt.launching,
	org.eclipse.debug.core,
	org.eclipse.jdt,
	org.eclipse.jdt.core,
	org.eclipse.core.resources,
	org.eclipse.ui.ide,
	org.eclipse.jdt.ui,
	org.eclipse.debug.ui,
	org.eclipse.jdt.debug.ui,
	org.eclipse.core.expressions,
	org.eclipse.ui.cheatsheets,
	org.eclipse.ui.console,
	org.eclipse.ui.navigator,
	org.eclipse.core.filesystem,
	org.apache.commons.logging
	Eclipse-LazyStart: true
	Bundle-ClassPath: classes/,lib/hadoop-core.jar,lib/jackson-core-asl-1.8.8.jar ,lib/jackson-mapper-asl-1.8.8.jar, lib/commons-configuration-1.6.jar,lib/commons-lang-2.4.jar, lib/commons-httpclient-3.0.1.jar,lib/commons-cli-1.2.jar
	Bundle-Vendor: Apache Hadoop

### 3. 执行ant（过程需要联网）
	ant D:\hadoop\hadoop-1.2.1\src\contrib\eclipse-plugin\build.xml
