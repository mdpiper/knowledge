# Miscellaneous

Notes that don't fit in other places.

## Downloading Dropbox links

I received a link to a Dropbox folder
that contained several files and subdirectories.
Here it is:
https://www.dropbox.com/sh/57dt68tx5v62tlq/AADTdScl_7VaPSoejJbgg4jUa?dl=0

Clicking on individual files to download them would be tedious.
Dropbox gives an option to zip and download everything in a folder
through a browser button,
but I need the files on a remote machine.
What I want is to `curl` or `wget` the files.

Note the `dl=0` query string at the end of the link.
Change the 0 to 1, and the folder can be zipped and downloaded with

    $ curl -L https://www.dropbox.com/sh/57dt68tx5v62tlq/AADTdScl_7VaPSoejJbgg4jUa?dl=1 > SiB_PCF85.zip

The `-L` follows redirects.

*Reference:*

* https://stackoverflow.com/a/33639744/1563298
