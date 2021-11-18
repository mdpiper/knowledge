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


## Import a Maven project into Eclipse

I have a Maven project ([csdms/bmi-java](https://github.com/csdms/bmi-java/))
that I want to import into Eclipse.
(Note that it was originally an Eclipse project.)
I tried a few different ways.
The Maven Eclipse plugin (m2e, installed by default with Eclipse) worked best.

Start by deleting the old `.classpath` file.
It caused problems.

In Eclipse, go to *File > Import...*
then select *Maven > Existing Maven Projects*.

This sets up correct `.classpath` and `.project` files.
Almost.
It didn't get the JRE location right.
To fix this, in Eclipse,
find *Java > Installed JREs* in the Preferences.
I had a JDK 1.7;
add a *MacOS X VM* (their language) using the similar path
to the JDK 1.8 on the system.


### Possible alternative

There's also a Maven command to set up an Eclipse project:
```
mvn eclipse:eclipse
```
but it may be out of date.

*References:*

* https://mkyong.com/maven/how-to-convert-maven-java-project-to-support-eclipse-ide/
* https://stackoverflow.com/a/5958520
* https://www.vogella.com/tutorials/EclipseMaven/article.html
