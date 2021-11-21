# Maven

[Maven](https://maven.apache.org/)
is a build tool for Java.
It supersedes `ant`.

See also my [ant](./ant.md), [java](./java.md), and [eclipse](./eclipse.md) notes.


## Build, test, doc, clean

The one command to rule them all:

    $ mvn verify

Run tests in a Maven project:
```
$ mvn test
```
Build a jarfile:
```
$ mvn package
```
Generate docs:
```
$ mvn javadoc:fix
$ mvn javadoc:javadoc
```
Check style:
```
$ mvn checkstyle:checkstyle
```
Clean all build artifacts:

    $ mvn clean


## Maven repository

Where does Maven store the code for all of its commands?  
In `~/.m2/repository`.


## Set up executable jar

The [Apache Maven Jar Plugin](https://maven.apache.org/plugins/maven-jar-plugin/index.html)
sets up a manifest in the jar file built for a project.
However, for a jar containing an executable main program,
the `mainClass` attribute must also be set.

For example, in a POM:
```xml
<plugin>
  <artifactId>maven-jar-plugin</artifactId>
  <version>3.0.2</version>
  <configuration>
    <archive>
      <manifest>
        <addDefaultImplementationEntries>true</addDefaultImplementationEntries>
        <addDefaultSpecificationEntries>true</addDefaultSpecificationEntries>
        <addClasspath>true</addClasspath>
        <mainClass>edu.colorado.csdms.heat.Heat</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
```

Build the project:

    $ mvn package

Then run the main program in the jar:

    $ java -jar target/bmi-example-java.jar

Or, better, use the Maven Exec Plugin described below.


## Third Party Plugins

There are a bunch of useful third-party plugins for Maven.

### Execute jar through mvn command

Use the [Maven Exec Plugin](http://www.mojohaus.org/exec-maven-plugin/index.html)
to call the executable main in a jar file.

Set up the POM with:
```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>3.0.0</version>
    <configuration>
        <mainClass>edu.colorado.csdms.heat.Heat</mainClass>
    </configuration>
</plugin>
```

Call with:

    $ mvn exec:java

