# GPG

A handy [cheatsheet](http://irtfweb.ifa.hawaii.edu/~lockhart/gpg/gpg-cs.html).

My old
[key](http://pgp.mit.edu:11371/pks/lookup?op=get&search=0xCCDFA6FF3AEFC393)
from RSI is still available at the
[MIT key server](http://pgp.mit.edu:11371/).

## Export a key pair + owner trust to files

For my key at CSDMS:
```bash
gpg --export --armor "Mark Piper (CSDMS)" > mp-csdms-public.key
gpg --export-secret-keys --armor "Mark Piper (CSDMS)" > mp-csdms-secret.key
gpg --export-ownertrust > mp-ownertrust.txt
```

## Import a key pair + owner trust from files

For my CSDMS key:
```bash
gpg --import mp-csdms-secret.key
gpg --import mp-csdms-public.key
gpg --import-ownertrust mp-ownertrust.txt
```

## List keys

List all the keys on my keyring:

	$ gpg --list-keys

List only keys belonging to a user:

	$ gpg --list-keys "Mark Piper (CSDMS)"

## Encrypt a file

Encrypt a file with:
```
gpg --encrypt mpiper-ds82.pdf
```
GnuPG will prompt for a username associated with one of my keys.
Enter either the key ID or the email address associated with the key.
End with an empty line.

## Decrypt a file

Decrypt a file with:
```
gpg --decrypt mpiper-ds82.pdf.gpg > mpiper-ds82.pdf
```
GnuPG will prompt for the password for the key used to encrypt the file.

## Set trust level for key

The following message appears for a key I copied from another computer:

> It is NOT certain that the key belongs to the person named in the user ID.

To correct this,
set the trust level of the key:
```
gpg --edit-key KEY_ID
gpg> trust
```
GnuPG will ask for a new trust level for the key.
I set it to 5 because I made the key.

When finished, quit:
```
gpg> quit
```
