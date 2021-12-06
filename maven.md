# Maven

[Maven](https://maven.apache.org/)
is a build tool for Java.
It supersedes `ant`.

See also my [ant](./ant.md), [java](./java.md), and [eclipse](./eclipse.md) notes.


## The Project Object Model (POM)

The Project Object Model (POM) file `pom.xml` stores all information
about a Maven project.


## The project lifecycle

Maven has three lifecycles for building and maintaining a project:

* clean
* build (or default)
* site

Each of these lifecycles has several steps, called *phases*,
which are executed in order.

A command can chain several phases across lifecycles.
For example,
to clean the local project directory,
step through the entire build process 
and install to the local Maven repository and
deploy to a remote repository, run:

    $ mvn clean install deploy

The details of the build lifecycle are given in the Maven docs (see below).

*References:*

* https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html


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
Clean all build artifacts in the project:

    $ mvn clean


## Maven repository

Where does Maven store the code for all of its plugins?  
In `~/.m2/repository`.
This is the local repository.
It's also the target for the `install` subcommand.


## Install and deploy

Install to the local repository:

    $ mvn install

Deploy to a remote repository (which calls install):

    $ mvn deploy 

To uninstall from the local repository,
just delete the directory tree.


## Set up an executable jar

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


## Install from a remote repository

To install a package from the
[CSDMS Apache Maven Repository](https://csdms.colorado.edu/repository/),
include the following in your project's `pom.xml`:
```xml
  <repositories>
    <repository>
        <id>csdms</id>
        <name>CSDMS Apache Maven Packages</name>
        <url>https://csdms.colorado.edu/repository</url>
        <releases><enabled>true</enabled></releases>
        <snapshots><enabled>true</enabled></snapshots>
    </repository>
  </repositories>
```
Include the package to install (e.g., `bmi`) with:
```xml
  <dependencies>
    <dependency>
      <groupId>edu.colorado.csdms</groupId>
      <artifactId>bmi</artifactId>
      <version>2.0</version>
      <type>jar</type>
      <scope>compile</scope>
    </dependency>
  </dependencies>
```
From your build machine, run

    $ mvn install

and the dependent package will be installed to your local repository.
