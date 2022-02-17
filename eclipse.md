# Eclipse

Eclipse is really nice for working with [Java](./java.md).
I prefer it over Emacs or vi.

See also my notes on using [Ant](./ant.md) and [Maven](./maven.md).

## Breakpoints

To hit a breakpoint, you need to execute a program from the _Debug_ menu,
not from the standard _Run_ menu.

## Hidden files

Eclipse doesn't show dot files by default in the _Package Explorer_.
Turn them on under "v" (down arrow, or view, menu) > Filters.

## The most wonderful menu item

Use *Project > Clean...* to fix everything that's wrong with a project.

## Import a Maven project into Eclipse

I have a Maven project ([csdms/bmi-java](https://github.com/csdms/bmi-java/))
that I want to import into Eclipse.
(Note that it was originally an Eclipse project.)
I tried a few different ways.
The Maven Eclipse plugin (m2e, installed by default with Eclipse) worked best.

Start by deleting the old `.classpath` file.
It caused problems, and it shouldn't be in version control.

In Eclipse, go to *File > Import...*
then select *Maven > Existing Maven Projects*.

This sets up correct `.classpath` and `.project` files.
Almost.
It didn't get the JRE location right.
To fix this, in Eclipse,
find *Java > Installed JREs* in the Preferences.
I had a JDK 7;
add a *MacOS X VM* (their language) using the similar path
to the JDK 8 on the system.


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


## Changing IDE fonts

The default font size in the Eclipse IDE is miniscule--I have to squint to read it.
And despite hundreds of preference settings, there isn't one to change it.
Based on [this tip](https://apple.stackexchange.com/a/309209),
I located the CSS file to edit;
in my case, it's
```
~/.p2/pool/plugins/org.eclipse.ui.themes_1.2.1500.v20210607-1102/css/common/e4_globalstyle.css
```

I added
```
* {
    font-size: calc(12px + 1.5vw);
}
```
and restarted Eclipse.

*References:*

* https://apple.stackexchange.com/a/309209
* https://stackoverflow.com/a/37836926
