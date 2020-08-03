Command-Line (CLI) Data Exploratio Tools
========================================

# Table of Content (ToC)


# Overview
This [README](https://github.com/bom4v/data-exploration/blob/master/README.md)
is part of a
[small project](https://github.com/bom4v/data-exploration)
aiming at documenting some of the data exploration command-line (CLI) tools
such as:
* [Parquet tools](https://github.com/apache/parquet-mr/tree/master/parquet-tools)
* [Apache Arrow](https://arrow.apache.org/)
  + [Arrow tools]()
  + [Install Apache Arrow](https://arrow.apache.org/install/)
* ORC tools

## See also
* [Apache Hadoop releases](https://hadoop.apache.org/releases.html)
  + [Install Hadoop in stand-alone mode on CentOS 7](https://www.vultr.com/docs/how-to-install-hadoop-in-stand-alone-mode-on-centos-7)

# Installation
Most of the tools depend on an underlying Hadoop installation.

## Java

### RedHat 7 / CentOS 7 / Amazon Linux 2

* Enable the EPEL Yum repository:
```bash
$ sudo yum -y install epel-release
```

* Install the head-less version of the 1.8 OpenJDK:
```bash
$ sudo yum -y install java-1.8.0-openjdk-headless
```

* Check the Java version:
```bash
$ java -version
openjdk version "1.8.0_252"
OpenJDK Runtime Environment (build 1.8.0_252-b09)
OpenJDK 64-Bit Server VM (build 25.252-b09, mixed mode)
```

## Hadoop
* Hadoop releases:
  + Annoucements: https://hadoop.apache.org/releases.html
  + Downloads: https://downloads.apache.org/hadoop/common/

* Install on RedHat/CentOS:
  https://www.vultr.com/docs/how-to-install-hadoop-in-stand-alone-mode-on-centos-7

* Hadoop versions greater than 3.0 may bring some incompatibilities with older
  tools like `parquet-tools` or `orc-tools`.
  Let us pick the latest 2.7.x relase, namely
  [2.7.7](https://hadoop.apache.org/release/2.7.7.html)
```bash
$ export HADOOP_VER="2.7.7"
$ sudo mkdir -p /opt/hadoop/tarballs && sudo chown -R ${USER} /opt/hadoop && \
  wget http://www-us.apache.org/dist/hadoop/common/hadoop-${HADOOP_VER}/hadoop-${HADOOP_VER}.tar.gz \
  -O /opt/hadoop/hadoop-${HADOOP_VER}.tar.gz
$ tar zxf /opt/hadoop/hadoop-${HADOOP_VER}.tar.gz -C /opt/hadoop && \
  mv /opt/hadoop/hadoop-${HADOOP_VER}.tar.gz /opt/hadoop/tarballs/
$ sed -i -e 's#export JAVA_HOME=\${JAVA_HOME}#export JAVA_HOME=\$\(readlink -f /usr/bin/java | sed "s:/bin/java::"\)#g' /opt/hadoop/hadoop-${HADOOP_VER}/etc/hadoop/hadoop-env.sh
```

* Add the Hadoop installation in the Shell set up, _e.g._:
```bash
$ cat >> ~/.bashrc << _EOF

# Java
export JAVA_HOME="\$(readlink -f /usr/bin/java | sed "s:/bin/java::")"

# Hadoop
HADOOP_VER="2.7.7"
HADOOP_ROOT="/opt/hadoop/hadoop-\${HADOOP_VER}"
export PATH="\${HADOOP_ROOT}/bin:\${PATH}"

_EOF
$ . ~/.bashrc
```

* Check that Hadoop is correctly installed:
```bash
$ hadoop version
Hadoop ${HADOOP_VER}
Subversion Unknown -r c1aad84bd27cd79c3d1a7dd58202a8c3ee1ed3ac
Compiled by stevel on 2018-07-18T22:47Z
Compiled with protoc 2.5.0
From source with checksum 792e15d20b12c74bd6f19a1fb886490
This command was run using /opt/hadoop/hadoop-${HADOOP_VER}/share/hadoop/common/hadoop-common-${HADOOP_VER}.jar
```

* Test the Hadoop Map-Reduce (MR) functionality:
```bash
$ rm -rf ~/tmp/hadoop && mkdir -p ~/tmp/hadoop/source && \
  cp -a /opt/hadoop/hadoop-${HADOOP_VER}/etc/hadoop/*.xml ~/tmp/hadoop/source/
$ hadoop jar /opt/hadoop/hadoop-${HADOOP_VER}/share/hadoop/mapreduce/hadoop-mapreduce-examples-${HADOOP_VER}.jar grep ~/tmp/hadoop/source ~/tmp/hadoop/output 'principal[.]*'
...
File System Counters
            FILE: Number of bytes read=1247812
            FILE: Number of bytes written=2336462
            FILE: Number of read operations=0
            FILE: Number of large read operations=0
            FILE: Number of write operations=0
    Map-Reduce Framework
            Map input records=2
            Map output records=2
            Map output bytes=37
            Map output materialized bytes=47
...
   File Input Format Counters
            Bytes Read=151
    File Output Format Counters
            Bytes Written=37
$ cat ~/tmp/hadoop/output/part*
6       principal
1       principal.
```

## Ant
* References:
  + Install (Ant 1.10.8): https://ant.apache.org/manual/index.html
  + Downloads: https://ant.apache.org/bindownload.cgi  

### Linux
* Donwload the Ant binary distribution:
```bash
$ ANT_VER="1.10.8"
$ sudo mkdir -p /opt/ant/tarballs && chown -R ${USER} /opt/ant && \
  wget https://downloads.apache.org/ant/binaries/apache-ant-${ANT_VER}-bin.tar.bz2 \
  -O /opt/ant/apache-ant-${ANT_VER}-bin.tar.bz2 && \
  tar jxf /opt/ant/apache-ant-${ANT_VER}-bin.tar.bz2 -C /opt/ant && \
  mv /opt/ant/apache-ant-${ANT_VER}-bin.tar.bz2 /opt/ant/tarballs/
```

* Add Ant set up in `~/.bashrc`:
```bash
$ cat >> ~/.bashrc << _EOF

# Ant
ANT_VER="1,10.8"
export ANT_HOME="/opt/ant/apache-ant-\${ANT_VER}"
export PATH="\${ANT_HOME}/bin:\${PATH}"

_EOF
. ~/.bashrc
```

* Check the version of Ant:
```bash
$ ant -version
Apache Ant(TM) version 1.10.8 compiled on May 10 2020
```

* Install Ant dependencies:
```bash
$ ant -f ${ANT_HOME}/fetch.xml -Ddest=system
```

* Copy the Ant Jar to `~/.ant/lib`:
```bash
$ mkdir -p ~/.ant/lib && cp -a ${ANT_HOME}/lib/* ~/.ant/lib/
```

## Thrift
* Reference: https://thrift-tutorial.readthedocs.io/en/latest/installation.html

### RedHat-8/CentOS-8/Fedora
* Use the native package manager to install Thrift:
```bash
$ sudo dnf -y install thrift
```

### General for Linux
* Reference: http://thrift.apache.org/download

* Install Ant, Bison and Yacc:
  + On RedHat-7/CentOS-7/Amazon-Linux-2:
```bash
$ sudo yum -y install ant automake libtool make byacc byaccj bison-runtime bison-devel flex g++ boost-devel
```
  + On Debian/Ubuntu:
```bash
$ sudo apt update && apt -y install ant automake bison flex g++ git libboost-all-dev libevent-dev libssl-dev libtool make pkg-config
```

* Configure the build for Thrift:
```bash
$ THRIFT_VER="0.12.0"
$ sudo mkdir -p /opt/thrift/tarballs && sudo chown -R ${USER} /opt/thrift && \
  wget "https://downloads.apache.org/thrift/${THRIFT_VER}/thrift-${THRIFT_VER}.tar.gz" \
  -O /opt/thrift/thrift-${THRIFT_VER}.tar.gz && \
  tar zxf /opt/thrift/thrift-${THRIFT_VER}.tar.gz -C /opt/thrift && \
  mv /opt/thrift/thrift-${THRIFT_VER}.tar.gz /opt/thrift/tarballs/
$ cd /opt/thrift/thrift-${THRIFT_VER} && \
  ./bootstrap.sh && \
  ./configure
```

* Fix the Maven repository scheme from http to https:
```bash
$ sed -i -e 's|mvn.repo=http://repo1.maven.org/maven2|mvn.repo=https://repo1.maven.org/maven2|g' lib/java/gradle.properties
```

* Build and install Thrift:
```bash
  make && \
  sudo make install
```

* Check the version of Thrift:
```bash
$ thrift --version
Thrift version 0.12.0
```

## Scala
* Reference:
  + Scala 2.12.8: https://www.scala-lang.org/download/2.12.8.html
  + Install SBT on Linux: https://www.scala-sbt.org/1.x/docs/Installing-sbt-on-Linux.html

### SBT
* Install SBT:
```bash
$ curl https://bintray.com/sbt/rpm/rpm | sudo tee /etc/yum.repos.d/bintray-sbt-rpm.repo
$ sudo yum -y install sbt
```

* Check SBT version:
```bash
sbt version
[info] welcome to sbt 1.3.13 (Oracle Corporation Java 1.8.0_252)
```

## SDKMan
* Reference: https://github.com/sdkman/sdkman-cli

* Install SDKMan:
```bash
$ curl -s https://get.sdkman.io | bash
$ . ~/.bashrc
```

* Install Scala:
```bash
$ sdk install scala 2.12.8
```

* Check Scala version:
```bash
$ scala -version
Scala code runner version 2.12.8 -- Copyright 2002-2018, LAMP/EPFL and Lightbend, Inc.
```

## Parquet tools

### MacOS
* Install with Homebrew:
```bash
$ brew install parquet-tools
```

### RedHat/CentOS
* There is a Jar binary package for the Parquet tools on
  [Maven Central](https://repo1.maven.org/maven2/org/apache/parquet/parquet-tools).
  The archive Jar can be downloaded and stored locally.
  Then `parquet-tools` simply becomes a Shell alias to
  `java -jar <jar-file> command `:
```bash
PQT_VER="${PQT_VER:=1.11.0}"; export PQT_VER
MVN_BASE="https://repo1.maven.org/maven2/org/apache"
MVN_DIR="parquet/parquet-tools/${PQT_VER}"
PQT_JAR="parquet-tools-${PQT_VER}.jar"
PQT_URL="${MVN_BASE}/${MVN_DIR}/${PQT_JAR}"
mkdir -p ~/dev/repo/jar \
  && wget ${PQT_URL} -O ~/dev/repo/jar/${PQT_JAR}
```

* If the Java version is not standard (_e.g._, on the BDP Prod EC2 instances),
  the Jar may have to be rebuilt locally from
  [the sources](https://github.com/apache/parquet-mr.git):
```bash
$ mkdir -p ~/tmp && git clone https://github.com/apache/parquet-mr.git ~/tmp/parquet-mr
$ cd ~/tmp/parquet-mr
```
  + Change the Thrift version in the `pom.xml` file
    - On RedHat-7/CentOS-7/Amazon-Linux-2:
```bash
$ thrift --version
Thrift version 0.9.1
```
    - On RedHat-8/CentOS-8:
```bash
$ Thrift version 0.13.0
```
  + Spot the following two lines in `pom.xml` and change the version
    of `thrift` accordingly:
```bash
$ grep -Hin "thrift.version" pom.xml
```
```xml
    <thrift.version>0.12.0</thrift.version>
    <format.thrift.version>0.12.0</format.thrift.version>
```
  + For instance, on RedHat-8/CentOS-8:
```bash
$ vi pom.xml
$ grep -Hin "thrift.version" pom.xml
pom.xml:97:    <thrift.version>0.13.0</thrift.version>
pom.xml:98:    <format.thrift.version>0.13.0</format.thrift.version>
pom.xml:492:            <exclude>thrift-${thrift.version}/**</exclude>
pom.xml:493:            <exclude>thrift-${thrift.version}.tar.gz</exclude>
pom.xml:617:        <thrift.version>0.9.0</thrift.version>
```
  + If there is an issue with `fasterxml.jackson` in doing so,
  https://stackoverflow.com/a/28050041/798053 may be worth a look
  (just add the corresponding `jackson` dependency rules at the end of
  the `pom.xml` file, itself at the top directory of the Parquet MR project).
  + [`jackson-core` on Maven Central](https://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-core/)
  + Add the following just before the `</dependencies>` ending tag:
```xml
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-core</artifactId>
      <version>2.9.10</version>
    </dependency>
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.10</version>
    </dependency>
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-annotations</artifactId>
      <version>2.9.10</version>
    </dependency>
```
  + Build all the Jar artifacts, including `parquet-tools`:
```bash
$ mvn clean package -Plocal
$ ls -laFh parquet-tools/target/parquet-tools-1.12.0-SNAPSHOT.jar
-rw-rw-r-- 1 user group 67M Aug  3 20:59 parquet-tools/target/parquet-tools-1.12.0-SNAPSHOT.jar
```
  + Copy the JAR package into a place where the user will find it:
```bash
$ cp -a parquet-tools/target/parquet-tools-1.12.0-SNAPSHOT.jar ~/dev/repo/jar/
```
* Any user can then add the following alias:
```bash
$ cat >> ~/.bash_aliases << _EOF

# Parquet tools
alias parquet-tools='java -jar ~/dev/repo/jar/parquet-tools-1.12.0-SNAPSHOT.jar

_EOF
$ . ~/.bash_aliases
```

* Install ORC tools:
$ sudo snap install orc
```

## ORC tools

### MacOS

```bash
$ brew install orc-tools
```

### RedHat/CentOS
* If needed, install snap:
```bash
$ sudo yum install -y epel-release
$ sudo yum makecache fast
```



