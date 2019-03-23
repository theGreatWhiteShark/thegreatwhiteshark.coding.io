+++
date = "2019-03-23T12:49:25+01:00"
draft = false
comment = true
type = "post"
slug = "email"
title = "Mutt: a command line email client"
subtitle = "Convenient handling of the emails in Linux"
tags = [ "Linux", "encryption" ]
description = "Did you ever secretly wished to handle email, you know, the nerdy way? No GUI, no mouse, just command line tools? Well, then prick your ears. In this post I will explain the basic setup to use **Mutt** with GPG encryption, multiple IMAP accounts, and **postfix** for sending outgoing email."
+++

But before we jump right to the configuration of
[Mutt](https://gitlab.com/muttmua/mutt) a short explanation of general
concepts in email delivery are required to clarify what Mutt is
actually doing and what not. 

When sending an email quite a number of different programs are
involved. Firstly, you write the mail in a **Mail User Agent (MUA)**,
basically a small program that allows you to compose and en- and
decrypt MIME messages (emails). It hands the mail to the **Mail
Transport Agent (MTA)**, a SMTP server handling, as the name already
suggests, the transport through your local system or the
internet. Depending on the location of the server the mailbox of the
receiver is located at also many MTAs can be involved in the transport.
But eventually all emails will end up at a **Mail Delivery Agent
(MDA)**, which accepts the mail and stores it into its local
database. In order to obtain the new email, the receiver has to use a
**Mail Retrieval Agent (MRA)** to download it to her local system and
than inspect it using a MUA.

What a mess! Why it's not as simple as a HTTP request done when e.g.
retrieving this blog post and doesn't it work using a single program
like Thunderbird? The point of having such an involved infrastructure
is because you want to receive your email regardless of if your
computer is running or not. In addition, you probably also want to
access it from multiple locations and devices. The reason you were
probably unaware of the multitude of programs involved is because big
programs, like Thunderbird, tend to not stick to the Unix principle
and try to do everything themselves causing them to be a combination
of a MUA, MRA, and MTA. This *won't* be the case for **Mutt**. Luckily
it features some IMAP capabilities to directly obtain emails from your
provider but it is intended to be just a MUA and we have to set up a
MTA ourselves to deliver the mails to our email providers. But don't
worry. Our software of choice, **postfix**, is quite easily to
configure, which will be covered in the end of this post. In case your
interested about how mail protocols and the individual parts in the
corresponding infrastructure do work, check out the
[documentation](https://gitlab.com/muttmua/mutt/wikis/MailConcept) of
Mutt.

# Mutt

Like many elderly Linux/Unix software Mutt is best to be compiled
freshly from source with all options you want to en- or disable
provided as additional arguments. But before we do, we need to install
some dependencies.

``` bash
sudo apt update
sudo apt install libncursesw5-dev libgpgme-dev gpgsm
```

Use the *ncurses* with a **w** at the end. This fixes charset and
display errors. At least at my end. **GPG made easy (GPGME)**  is an API
wrapper around the command line `gpg` program. It also is able to
support S/MIME, which will be covered internally by the *gpgsm*
package. Both tools make it super easy to use encryption with GPG in
Mutt. Now, it's time to compile the source.

``` bash
## Obtain a copy of the Mutt source code
git clone https://gitlab.com/muttmua/mutt
cd mutt
## Compile it with a number of options.
./configure --enable-pgp --enable-gpgme --enable-compressed --enable-hcache --enable-imap --enable-sidebar --with-gnutls --with-curses=/usr/lib/x86_64-linux-gnu/
```

In this custom compilations we enabled encryption using GPG with
`--enable-pgp --enable-gpgme`, asked Mutt to optimize its resource
management by compressing the emails with `--enable-compressed`, and
to speed up mail inspection by storing some metadata of the recently
explored emails with `--enable-hcache`. The 
`--enable-imap --enable-sidebar --with-gnutls` arguments allow us to
directly retrieve emails from a provider using the IMAP protocol
encrypted with GnuTLS and to display various content in a sidebar. The
`--with-curses` option was important for me to render non-ASCII
symbols properly.

If you for some reason want to use the version of Mutt provided by
the package system of your operation system, you can install it via
``` bash
sudo apt update
sudo apt install mutt
```
But some of the features described in the following may not be enabled
in this default version.

### IMAP setup

First things first. Let's start with getting access to our emails
using Mutt. Classically, you would install a MDA, like
[fetchmail](http://www.fetchmail.info/),
[getmail](http://pyropus.ca/software/getmail/), or
[retchmail](http://freecode.com/projects/retchmail), download all
emails into a local mailbox of the
e.g. [Maildir](https://cr.yp.to/proto/maildir.html) format, and tell
Mutt to use this folder. It sure has the benefit of being able to
automatically process incoming mail with programs like
[SpamAssassin](https://spamassassin.apache.org/) but such setup is too
involved for us right now. Instead, we will just retrieve the
individual mails using IMAP.

We start with some general configuration
``` bash
### IMAP
# Activate TLS
set ssl_starttls=yes
# Always use SSL when connecting to server
set ssl_force_tls=yes
# Don't wait till a manual entering of the mailbox occurred
unset imap_passive
# Poll every 15 seconds
set mail_check=15
# Keep a cache of the headers for faster loading
set header_cache=~/.muttHeaderCache
# Display the download progress every 5KB
set net_inc=5
# View the mail in threaded mode
set sort = threads
set sort_aux = reverse-last-date-received
# Only notify me for new mails since the last poll and not about all
# new mails in the mailbox.
set mail_check_recent = yes
# Check for new mail in the current mailbox.
set imap_idle = yes
```
You can just copy-paste these lines into your *~/.mutt/muttrc* as they
enable encrypted communication and just set some general stuff. A full
list of all options can be found in the official
[documentation](http://www.mutt.org/doc/manual/).

Next, we have to add all settings for a specific email account of
ours, e.g. Gmail or Posteo.

``` bash
# General settings
set from = "USER@googlemail.com"
set folder = "imaps://imap.gmail.com"
set record = "=Sent"
set spoolfile = "=Inbox"
set postponed = "=%5BGoogle%20Mail%5D/Entw%26APw-rfe"
set trash = "=Trash"

set use_envelope_from
set envelope_from_address = "USER@googlemail.com"

# Retrieve the password from the 'pass' password manager.
set my_pw_gmail=`pass show Email/gmail`

# IMAP
set imap_user = "USER@googlemail.com"
set imap_pass = $my_pw_gmail

### Custom macros
# Move to the 'Alle Nachrichten' folder
set my_gmail_location_all_messages = "=%5BGoogle%20Mail%5D/Alle%20Nachrichten"
macro index,pager \Ca "<change-folder>$my_gmail_location_all_messages<enter>"

# Monitor incoming mail from the following folders
set my_gmail_location_xxx = "=%5BGoogle%20Mail%5D/XXX"
set my_gmail_location_yyy = "=%5BGoogle%20Mail%5D/YYY"
mailboxes $spoolfile $postponed $record $trash            \
	  $my_gmail_location_all_messages                     \
	  $my_gmail_location_xxx $my_gmail_location_yyy

# Custom status bar
set status_format = "-%r-Gmail: %f [Msgs:%?M?%M/?%m%?n? New:%n?%?o? Old:%o?%?d? Del:%d?%?F? Flag:%F?%?t? Tag:%t?%?p? Post:%p?%?b? Inc:%b?%?l? %l?]---(%s/%S)-%>-(%P)---"
```

That's quite a lot! Let's go through it line by line. The variable
`folder` is maybe the most important one and specifies the
particular mailbox we want to use. Other locations, like `record`,
`spoolfile`, `postponed`, and `trash`, are default locations, which
are almost always in use regardless of your provider. Please note that
you have to enter the proper name for *your* mailbox. The English
version of the `postponed` folder will most properly be different from
my German one.

The `use_envelope_from` and `envelope_from_address` override the
`from` field in an email and ensure the receiver does know it was you
who sent it. Without these options I had problem with some of my
accounts. That's why I stick to it by default.

To be able to identify yourself to the IMAP server, you have to
provide both a username and a password. You can decide to type both of
them either every time you open the mail folder in Mutt, which is a
quite tedious thing to do, or to store them in the `imap_user` and
`imap_pass` variables. But then again, you maybe do not want your
password to be written in plain text in an unencrypted configuration
file. If you by accident upload it to some server or post it on a
mailing list, you are in deep trouble. To solve this issue, I store
the password using the Linux program `pass`, which is explained in a
[previous
post](/thegreatwhiteshark.coding.io/encryption-and-backup). Regardless
of the mail account you use, you will be prompted for your GPG
passphrase and only have to reenter it if more than 5 minutes after
the last call to `pass` did passed.

The remainder is some convenience configuration, which establishes the
shortcut CTRL+a to jump to the folder containing all mails, sets the
`mailboxes` variable to show a couple of subfolders including the
number of unread mails in the sidebar, and defines a custom status
bar. The latter is important if you handle multiple IMAP accounts and
doesn't want to loose sight of which one you are handling.

### Using multiple IMAP accounts

Usually, people do have quite a number of email addresses. But how to
use all of them within Mutt? I would recommend to write for each
account a separate configuration file like above and store them in
files like *~/.mutt/account.0*, *~/.mutt/account.1* etc. Then we
define a shortcut in the *~/.mutt/muttrc* to load the individual files
and thus to switch between the folders using e.g. the F5, F6, and F7
keys, and pick one of them to act as our default account.

``` bash
## Keyboard shortcuts for switching between different accounts.
macro index <f5> '<sync-mailbox><enter-command>source ~/.mutt/account.0<enter><change-folder>!<enter>'
macro index <f6> '<sync-mailbox><enter-command>source ~/.mutt/account.1<enter><change-folder>!<enter>'
macro index <f7> '<sync-mailbox><enter-command>source ~/.mutt/account.2<enter><change-folder>!<enter>'

# Set the default account initialized during startup
source ~/.mutt/account.1
```

To ensure everything will work properly and the configuration of one
account does not interfere with the settings of another, you should
add the following lines to the beginning of each account
configuration.

``` bash
# Reset variables specified in other accounts
unmailboxes *
unset signature
unset realname
unset use_envelope_from
```


### GPG setup

Of course we still want to encrypt our mails using GPG and I promised
it to be very easy. But first we have to enter a couple of lines to
our *~/.mutt/muttrc*.

``` bash
### Use GPG en|decryption
# Enable the automatic key retrieval of GPG
source ~/.mutt/gpg.rc
# Default key used during the encryption
set pgp_default_key = "KEY_ID"
# Uses 'gpg-agent'. Since gpg > version 2.1 this option has to be set.
set pgp_use_gpg_agent = yes
# Use automated encryption whenever the public keys of all recipients
# are in the keyring.
set crypt_opportunistic_encrypt = yes
# Reply encrypted to encrypted mails
set crypt_replyencrypt = yes
# Automatically verify signatures upon opening an email.
set crypt_verify_sig = yes
# Postponed messages that are marked for encruption will be
# self-encrypted.
set postpone_encrypt = yes
```

Be sure to replace *KEY_ID* with the ID of your personal key. You can
find it using `gpg --list-secret-keys`. In addition, we also need the
corresponding *~/.mutt/gpg.rc* file.

{{< highlight bash >}}
### beginning of ~/.mutt/gpg.rc
#
# Command formats for gpg.
# 
# $Id$
# 
# %p    The empty string when no passphrase is needed,
#       the string "PGPPASSFD=0" if one is needed.
# 
#       This is mostly used in conditional % sequences.
# 
# %f    Most PGP commands operate on a single file or a file
#       containing a message.  %f expands to this file's name.
# 
# %s    When verifying signatures, there is another temporary file
#       containing the detached signature.  %s expands to this
#       file's name.
# 
# %a    In "signing" contexts, this expands to the value of the
#       configuration variable $pgp_sign_as.  You probably need to
#       use this within a conditional % sequence.
# 
# %r    In many contexts, mutt passes key IDs to pgp.  %r expands to
#       a list of key IDs.

# Note that we explicitly set the comment armor header since GnuPG, when used
# in some localiaztion environments, generates 8bit data in that header, thereby
# breaking PGP/MIME.

# Note from the Debian mutt maintainers: starting from 1.7.0-2 GPGME is enabled
# by default. More info in NEWS.Debian.
# THe pgp_* commands are left here for people who disable gpgme in their
# ~/.muttrc
set crypt_use_gpgme=yes

# Note from the Debian mutt maintainers: the addition of
# "--pinentry-mode loopback" breaks gpgv1 compatiblity, if you need to use gpgv1
# remove that statement.

# decode application/pgp
set pgp_decode_command="gpg --status-fd=2 %?p?--passphrase-fd 0 --pinentry-mode=loopback? --no-verbose --quiet --batch --output - %f"

# verify a pgp/mime signature
set pgp_verify_command="gpg --status-fd=2 --no-verbose --quiet --batch --output - --verify %s %f"

# decrypt a pgp/mime attachment
set pgp_decrypt_command="gpg --status-fd=2 %?p?--passphrase-fd 0 --pinentry-mode=loopback? --no-verbose --quiet --batch --output - %f"

# create a pgp/mime signed attachment
set pgp_sign_command="gpg --no-verbose --batch --quiet --output - %?p?--passphrase-fd 0 --pinentry-mode=loopback? --armor --detach-sign --textmode %?a?-u %a? %f"

# create a application/pgp signed (old-style) message
set pgp_clearsign_command="gpg --no-verbose --batch --quiet --output - %?p?--passphrase-fd 0 --pinentry-mode=loopback? --armor --textmode --clearsign %?a?-u %a? %f"

# create a pgp/mime encrypted attachment
set pgp_encrypt_only_command="/usr/lib/mutt/pgpewrap gpg --batch --quiet --no-verbose --output - --encrypt --textmode --armor --always-trust -- -r %r -- %f"

# create a pgp/mime encrypted and signed attachment
set pgp_encrypt_sign_command="/usr/lib/mutt/pgpewrap gpg %?p?--passphrase-fd 0 --pinentry-mode=loopback? --batch --quiet --no-verbose --textmode --output - --encrypt --sign %?a?-u %a? --armor --always-trust -- -r %r -- %f"

# import a key into the public key ring
set pgp_import_command="gpg --no-verbose --import %f"

# export a key from the public key ring
set pgp_export_command="gpg --no-verbose --export --armor %r"

# verify a key
set pgp_verify_key_command="gpg --verbose --batch --fingerprint --check-sigs %r"

# read in the public key ring
set pgp_list_pubring_command="gpg --no-verbose --batch --quiet --with-colons --with-fingerprint --with-fingerprint --list-keys %r"

# read in the secret key ring
set pgp_list_secring_command="gpg --no-verbose --batch --quiet --with-colons --with-fingerprint --with-fingerprint --list-secret-keys %r"

# fetch keys
# set pgp_getkeys_command="pkspxycwrap %r"

# pattern for good signature - may need to be adapted to locale!

# set pgp_good_sign="^gpgv?: Good signature from "

# OK, here's a version which uses gnupg's message catalog:
# set pgp_good_sign="`gettext -d gnupg -s 'Good signature from "' | tr -d '"'`"

# This version uses --status-fd messages
set pgp_good_sign="^\\[GNUPG:\\] GOODSIG"

# pattern to verify a decryption occurred
set pgp_decryption_okay="^\\[GNUPG:\\] DECRYPTION_OKAY"

### end off ~/.mutt/gpg.rc
{{< / highlight >}}

### Various stuff

Here are a some additional configurations to ease the handling of
Mutt. First, we will set up the sidebar and an easy navigation through
it.

``` bash
### Configuring the sidebar
# Show the sidebar (all folders defined in the 'mailbox' variable)
set sidebar_visible = yes
# Don't print the full path of the individual folders
set sidebar_short_path = yes
# Width in number of characters
set sidebar_width = 15
# Keybindings to handle the sidebar
# macro index,pager \C<Up> '<sidebar-prev>'		"move up in the sidebar"
macro index,pager i '<sidebar-prev>'		"move down in the sidebar"
macro index,pager o '<sidebar-next>'		"move down in the sidebar"
macro index,pager p '<sidebar-open>'		"open a folder from the sidebar"
macro index,pager <ESC>i '<sidebar-page-up>'	"move one page up in the sidebar"
macro index,pager <ESC>o '<sidebar-page-down>'	"move one page down in
the sidebar"
```

In addition, we will use **Emacs** as our `editor` of choice for
composing emails. You probably have a quite complex *~/.emacs*
file, which makes the program on the one hand quite flexible and
powerful but on the other hand also slow to start up. Therefore, you
should either boil down your configuration to the most essential stuff
or just use the my [minimal
one](https://gitlab.com/theGreatWhiteShark/configurations-and-scripts/blob/master/emacs/.emacs-mutt).

``` bash
### Various
# Use Emacs as default editor
set editor = "emacs %s --no-init-file --no-x-resources --no-site-file --no-window-system --load ~/.mutt/.emacs-mutt -f mutt-mode"
# Use sendmail to transfer mails via SMTP to an MTA
set sendmail="/usr/sbin/sendmail -oi -oem"
# Use a custom color theming
source ~/.mutt/colors
# Include messages in replies
set include
# Don't ask me about postponing a message when writing a new email.
set recall = ask-no
```

Also we use some custom coloring

``` bash
### Beginning of ~/.mutt/colors
### Custom colors used within mutt
# Default colour definitions
color quoted     red          black
color indicator  white	      brightblack
color error      brightyellow  black
color message    brightyellow black
color search     brightwhite  magenta
color status     black 	      brightyellow
color tree       red          black
color normal     white        black

set header_color_partial = yes
# # Colours for items in the reader
color header brightwhite black "^(From|Subject):"

# Colours for items in the index
color index brightwhite black ~O # old messages
color index brightwhite black ~N # new messages
color index white 	brightred ~D # deleted messages
color index brightyellow	black ~T # tagged messages

# Highbrights inside the body of a message.
# URLs
color body brightblue  black "(http|https|ftp|news|telnet|finger)://[^ \">\t\r\n]*"
color body brightblue  black "mailto:[-a-z_0-9.]+@[-a-z_0-9.]+"
color body brightblue  black "news:[^ \">\t\r\n]*"
# email addresses
color body brightblue  black "[-a-z_0-9.%$]+@[-a-z_0-9.]+\\.[-a-z][-a-z]+"
### end of ~/.mutt/colors
```

The full configuration I use with Mutt can be found in
[here](https://gitlab.com/theGreatWhiteShark/configurations-and-scripts/tree/master/mutt). If
you also plan to upload yours, be sure to encrypt all files containing
either passwords or other confidential stuff as well as all your files
listing email addresses, be it of friends, mailing lists, or even
yourself. Unobscured addresses should be never uploaded into public
locations.

# Postfix

Last but not least, we still have to install a MTA to be able to send
our emails. In here we will use [postfix](http://www.postfix.org/).

```bash
sudo apt update
sudo apt install mutt postfix
```

It will replace the default MTA/MDA `sendmail` by linking the binary
*/usr/sbin/sendmail* to its own variant capable of handling the same
input arguments as the original.

The main configuration of postfix can be found in
*/etc/postfix/main.cf*. We will just add several lines and keep the
rest of the file intact.

``` bash
### SMTP spass
# Enables client-side authentication
smtp_sasl_auth_enable = yes
# Ensures encryption
smtp_tls_security_level = encrypt
# Removes prohibition on plaintext passwords
smtp_sasl_tls_security_options = noanonymous
# Send username and password information to the mail gateway server.
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
# Support multiple accounts
smtp_sender_dependent_authentication = yes
sender_dependent_relayhost_maps = hash:/etc/postfix/sender_relay
# Use the Gmail one as the default
relayhost = [smtp.gmail.com]
```

In order to use postfix with state-of-the-art email providers we have
to activate encryption and supply it with our credentials. The file
*/etc/postfix/sender_relay* specifies which outgoing SMTP server to
use for which email account. This is key when using multiple ones.

``` bash
### beginning of /etc/postfix/sender_relay
# Use different providers for different senders
USER@ladi.da				[ladi.da]:587
USER@gmx.net				[mail.gmx.net]
### end of /etc/postfix/sender_relay
```

The corresponding passwords are stored in the
*/etc/postfix/sasl_password* file.

``` bash
### beginning of /etc/postfix/sasl_passwd
# Use different authentication for different senders
USER@ladi.da				USER:PASSWORD
USER@gmx.net				USER@gmx.net:PASSWORD

# Login information for the defaul relay
# Destination			credentials
[smtp.gmail.com]     	USER@googlemail.com:PASSWORD
### end of /etc/postfix/sasl_passwd
```

In both the passwords and addresses have to be inserted in
plaintext. Be sure to configure the permissions properly or other
users can read them too!

After modifying those files, run the following commands to write the
changes into the `postfix` database. The actual files won't be
read/touched.

``` bash
sudo postmap /etc/postfix/sender_relay
sudo postmap /etc/postfix/sasl_passwd
```

Finally, make `postfix` aware of the changes by invoking

``` bash
sudo postfix reload
```

Now, everything is set and done and you should be able to do all your
daily email stuff with Mutt.
