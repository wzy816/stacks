# scala

## sbt

```bash
# java 17 ready
java -version

# scala
sdk list scala

# install with sdk 1.6.2
sdk install sbt

# check version
sbt sbtVersion

# open terminal
sbt
sbt -Dsbt.override.build.repos=true

# show class path
sbt 'export fullClasspath'
```

## terminal

```bash
# clean
clean

# run
run

# continuous compile
~ compile

# build jar
package

# run with params 运行带参数
run <param1> <param2>

# check plugins
plugins

# show subprojects of multiple projects
projects

# select subproject
project <my_subproject>

# generate docs in target/scala-2.11/api
doc

# check dependencies
evicted

# use sbt-assembly to build all subprojects
assembly

# build subproject fat jar only
<my_subproject>/assembly
```

## jar

```bash
# show content
jar tvf <path_to_my_jar>
```

## repl

```bash
# with jar
scala -cp
scala -classpath [path/to/jar]
java -cp "/path/to/jar1:/path/to/jar2" main.class
```

## timezone

https://garygregory.wordpress.com/2013/06/18/what-are-the-java-timezone-ids/
