# IDL

Notes on using IDL
I'm not sure whether to be sad or happy that I have to do this.

## Path management

Permanently include a directory (and everything beneath it)
in your path:

    pref_set, 'IDL_PATH', '<IDL_DEFAULT>:+/some/directory/path', /commit

Include a directory (and everything beneath it) in your path
for the duration of an IDL session:

    !path += ':+/some/directory/path'

My `add_path` routine does something similar,
but in a slightly more sophisticated manner.
