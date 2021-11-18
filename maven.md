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
