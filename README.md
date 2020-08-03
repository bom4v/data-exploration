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

# Installation

## Parquet tools

### MacOS

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
  the Jar may have to be rebuilt locally from the sources
  (with `mvn clean package -Plocal`).
  If there is an issue with `fasterxml.jackson` in doing so,
  https://stackoverflow.com/a/28050041/798053 may be worth a look
  (just add the corresponding `jackson` dependency rules at the end of
  the `pom.xml` file, itself at the top directory of the Parquet MR project).
  + [`jackson-core` on Maven Central](https://repo1.maven.org/maven2/com/fasterxml/jackson/core/jackson-core/)
  + Add the following just before the `</dependencies>` ending tag:
```xml
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-core</artifactId>
      <version>2.5.0</version>
    </dependency>
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.5.0</version>
    </dependency>
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-annotations</artifactId>
      <version>2.5.0</version>
    </dependency>
```

* Any user can then add the following alias:
```bash
$ cat >> ~/.bash_aliases << _EOF

# Parquet tools
alias parquet-tools='java -jar ~/dev/repo/jar/parquet-tools-1.11.0.jar'

_EOF
$ . ~/.bash_aliases
```

* Install ORC tools:
```bash
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



