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
    font-size: 18;
}
```
and restarted Eclipse.

*References:*

* https://apple.stackexchange.com/a/309209
