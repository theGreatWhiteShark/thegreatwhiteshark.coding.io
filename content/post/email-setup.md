+++
date = "2019-01-14T12:49:25+01:00"
draft = true
comment = true
type = "post"
slug = "email"
title = "Customized email setup in Devuan"
subtitle = "Using mutt + postfix + emacs + fetchmail + gpg"
tags = [ "" ]
description = ""
+++

# Prerequisites

``` bash
sudo apt update
sudo apt install mutt postfix
```

# General

About MUA, MTA, MDA, MSA, MRA

### MTA

- [sendmail](http://www.sendmail.org/)
  Not as many features as the others. But is using D-Bus.
- [qmail](https://cr.yp.to/qmail.html)


# Postfix

It will replace the default MTA/MDA `sendmail` by replacing its binary
in /usr/sbin with a link to its own variant capable of handling the
input argument of the original `sendmail` program.

Small guide through /etc/postfix/main.cf

To use multiple accounts -> /etc/postfix/sender_relay &
/etc/postfix/sasl_passwd

In both the password has to be inserted in plaintext. Be sure to
configure the permissions properly or other users can read them too!

After modifying those files, run the following commands to write the
changes into a data base. The actual files won't be read/touched by
`postfix`.

``` bash
sudo postmap /etc/postfix/sender_relay
sudo postmap /etc/postfix/sasl_passwd
```

Make `postfix` aware of the changes by invoking

``` bash
sudo postfix reload
```

# mutt

``` bash
sudo apt install libncursesw5-dev libgpgme-dev gpgsm
```

Use the *ncurses* with a **w** at the end. This fixes charset and
display errors. At least at my end.

*GPGME* - GPG made easy is an API wrapper around the command line gpg
program. It also is able to support S/MIME, which will be covered
internally by the *gpgsm* package.

``` bash
./configure --enable-pgp --enable-gpgme --enable-compressed --enable-hcache --enable-smtp --enable-imap --enable-sidebar --with-gnutls --with-curses=/usr/lib/x86_64-linux-gnu/
```

 # GPG
 
 ``` bash
 sudo apt install dirmngr
 sudo apt-mark auto gpgsm dirmngr
 ```
 
 *dirmngr* takes care of talking to the GPG keyserver since version
 2.1 All the examples you can find in the internet using the
 *--keyserver* argument to `gpg` are obsolete.
 
 `apt-mark` is a unified interface to set the properties of installed
 package. Using the *auto* command, they are handled as installed
 automatically and will be thus issued for the autoremoval as soon as
 no other package is depending on them anymore.
 
 Generate key
 
 Publish key
 
 Create revokation certificate.
 
 Export and import. Be sure to `shred` the copied private key.

To use it with `pass`:

```
gpg --edit-key <KEY_ID>
gpg> trust
```

You will be asked to select the trust level from the following:

```
1 = I don't know or won't say
2 = I do NOT trust
3 = I trust marginally
4 = I trust fully
5 = I trust ultimately
m = back to the main menu
```

I selected 5 since I created the key so of course I trust it ultimately :). It will ask you to confirm your decision:

```
Your decision? 5
Do you really want to set this key to ultimate trust? (y/N) y
```

After confirming, you should be able to encrypt using that key.


[Introduction](https://easyengine.io/tutorials/linux/gpg-keys) into GPG.


Encrypt a single file

``` bash
gpg2 --encrypt -a --recipient $GPGKEY file
```
$ 


                               
``` bash
gpg2 --decrypt --output file file.asc
```

# Getmail

``` bash
sudo apt install getmail4
```

``` bash
mkdir -m 0700 ~/.getmail
```
https://www.linode.com/docs/tools-reference/tools/schedule-tasks-with-cron/
https://www.linode.com/docs/email/clients/retrieve-email-using-getmail/
