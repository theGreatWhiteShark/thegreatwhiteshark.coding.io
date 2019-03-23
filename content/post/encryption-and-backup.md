+++
date = "2019-03-22T13:49:50+01:00"
draft = false
comment = true
type = "post"
slug = "encryption-and-backup"
title = "Encryption, password management, and backups in Linux"
subtitle = "An easy introduction to some of the most important tools and concepts"
tags = [ "Linux", "data", "encryption" ]
description = "Be it malicious crackers, evil governments, or all-devouring US companies, we have to protect our data from their greedy grasps. But in the beginning it seems to be such a hard task. There are tons of tools and one does neither want to spend several weeks on reading documentation nor to use insecure ones. To alleviate this pain, I will give a short introduction by presenting four simple but essential tools: **dm-crypt/LUKS** for hard disk encryption, **GnuPG** for encrypting individual files, emails etc., **borg** to securely and efficiently backup your data, and **pass** to manage and store your passwords."
+++

Since at least the revelations of Edward Snowden we know that
encryption is not just an important but also a quite pressing
issue. But, unfortunately, there is no one-size-fits-all algorithm
that works with all kinds of data and communication protocols. We are
therefore bound to at least peek into different topics of this rather
complex field. But don't be afraid. I wrote this introduction with
people at a beginner level in Linux in mind and won't touch a single
bit of mathematical theory on encryption.

Most of the tools presented here work independent of each other except
of `pass`, which depends on a proper *GnuPG* setup. For
the remaining parts you don't have to read the post as a whole but can
pick those topics instead you are most interested in. We will start with
the encryption of entire disks using
[dm-crypt/LUKS](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt). Then
a guide to set up the [GnuPG](https://gnupg.org/) either from scratch
or by importing an existing key is presented in addition with some
remarks of how to use it for encrypting files and emails. The same
encryption mechanism will also be used when backing up your system
with [borg](https://borgbackup.readthedocs.io/en/stable/) and storing
and managing your passwords using
[pass](https://www.passwordstore.org/), which both will be explained
in the two remaining sections.

# dm-crypt/LUKS encryption

Whether you hard disk or laptop gets stolen, you have to save personal
data under the jurisdiction of the [General Data Protection
Regulation](https://en.wikipedia.org/wiki/General_Data_Protection_Regulation)
(in Europe), or there is a police search and massive seizures in your
local hackspace or at home, you don't want your data to be accessed by
unauthorized persons. But since you might use these devices on a daily
basis and maybe even in combination with different platforms, you want
the tools to be both convenient and independent of a particular
operation system. 

This is where
[dm-crypt/LUKS](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt)
and its Linux frontend `cryptsetup` enters. There is an implementation
for Windows systems, although I never tried it, and it allows you to
access and use your hard disk as usual after providing your password
once per startup independent on your particular choice of file system
of that disk. It is also used when you decide to encrypt your home
folder during the installation of Linux.

To encrypt a disk using LUKS, we will use the following command in a
bash shell. Note that you have to exchange the */dev/sdX* with the
particular file representation. Use the commands `lsblk -f` or 
`ls -ctAl /dev/disk/by-id` to find the disk you are looking for. Also
note that after this step you can not access the data present on your
disk anymore. So be sure to back them up properly.

{{< highlight Bash >}}
sudo cryptsetup luksFormat /dev/sdX
{{< / highlight >}}

The command will ask you twice for a passphrase required to decrypt
the device later on. Be sure to not forget this one!

To decrypt disk, we will use the `cryptsetup luksOpen` command.

{{< highlight Bash>}}
sudo cryptsetup luksOpen /dev/sdX label
{{< / highlight >}}

It will use the device mapper to allow the access to the encrypted
device via the path */dev/mapper/label*. All data read from or
written to this location will be de- or encrypted on the fly. So,
accessing such an encrypted device is bound to be both slower and to
consume more energy.

If you had already set up a file system on the device, jump right to
the mounting of the device and use it like an unencrypted one. If you,
on the other hand, just used the `cryptsetup luksFormat` command, the
device has no file system and we have to create one. Which one is
totally up to you. You can either create the default
[ext4](https://en.wikipedia.org/wiki/Ext4) one using `mkfs.ext4` or a
more advanced one like [btrfs](https://en.wikipedia.org/wiki/Btrfs)
with `mkfs.btrfs`. Optionally, you can override all existing data
remaining on the disk (hidden and inaccessible).

{{< highlight Bash >}}
## Override all existing data
sudo dd if=/dev/zero of=/dev/mapper/label
## Create a file system on the device.
sudo mkfs.btrfs /dev/mapper/label
{{< / highlight >}}

Now you can mount the device and use it as usual.

{{< highlight Bash >}}
sudo mount /dev/mapper/label /mnt
{{< / highlight >}}

# GPG

We know how to encrypt the whole disk with all data it contains. But
as soon as we use it, the data is not encrypted anymore. Also when we
either want to transmit the data via a public network or upload it to
some sort of server, we definitely want it to be protected. But how to
do it? One very prominent way is the asymmetric encryption using
GPG. Asymmetric, because each party has both a *public key* it has to
distribute to all persons it want to share the data with and a *secret
key* no one else should ever have access to. Using the public key of
another person a file can be encrypted in such a way it can only be
decrypted using by the person itself with the help of the
corresponding secret key. By using your own public key, you can also
encrypt a file in such a way only you can access its content. This way
you can upload sensitive and confidential data to servers like
e.g. GitLab. But first, we have to generate a key if you do not have
one yet.
 
### Generate key

We will do so using the command below. If you have `gpg2` installed,
don't hesitate using it instead.

{{< highlight Bash >}}
gpg --full-gen-key
{{< / highlight >}}

You will be asked a couple of questions. If you have no experience,
just choose 

> Please select what kind of key you want:
> (1) RSA and RSA (default)

a keysize of 4096

> What keysize do you want? (3072)
> 4096

and expiration date in the near future. Enter your email address, your
real name, and a comment if you wish to.

Next, pick a secure passphrase (long! but also big and small letters
and punctuation signs). Since we will use this passphrase to access
our password store, using `pass generate`, which will be covered
later, unfortunately is off the table. So, be sure to set one you will
still remember in years.
 
### Publish key

In order for others to encrypt data, like emails, with your public key,
they, of course, have to get a hold of it first. A common thing to do
is to upload it to a GPG keyserver. The command handling it does live
outside of the GPG package nowadays and is not installed per default
on most devices.

{{< highlight Bash >}}
sudo apt install dirmngr
{{< / highlight >}}
 
`dirmngr` takes care of talking to the GPG keyserver since version
2.1. All the examples you can find in the internet using the
`--keyserver` argument to `gpg` are obsolete. 

Now, the following command will upload the public key associated with
the key ID \<KEY\_ID\>. You can find the ID by running `gpg --list-keys`.

{{< highlight Bash >}}
gpg --send-keys <KEY_ID>
{{< / highlight >}}

### Create a revokation certificate.

But what if you loose your secret key or the corresponding passphrase?
People will use your public key from one of the key servers and keep
sending you encrypted files and emails you are not able to open
anymore. To get your key removed from the server, you need a so-called
revokation certificate. It's best practice to generate it right away
and store it on all your private hard disks and backups.

{{< highlight Bash >}}
gpg --output revoke.asc --gen-revoke <KEY_ID>
{{< / highlight >}}
 
### Export and import

Instead of generating a new key, you can also import your key
generated on another device. But be sure to
[shred](https://linux.die.net/man/1/shred) any intermediate copies of
your private key.

To be able to use it properly with local applications, you have to
tell the system the secret key is actually yours.

{{< highlight bash >}}
gpg --edit-key <KEY_ID>
gpg> trust
{{< / highlight >}}

You will be asked to select the trust level from one of following
options:

> 1 = I don't know or won't say \\
> 2 = I do NOT trust \\
> 3 = I trust marginally  \\
> 4 = I trust fully \\
> 5 = I trust ultimately \\
> m = back to the main menu

For further information check out the following
[link](https://easyengine.io/tutorials/linux/gpg-keys).

### Encrypt and decrypt a single file

Now, let's use our own public key to encrypt a file just for
ourselves.

{{< highlight bash >}}
## Encrypting a file
gpg --encrypt -a --recipient <KEY_ID> file
## Decrypting a file
gpg --decrypt --output file file.asc
{{< / highlight >}}

When en- and decrypting emails using Thunderbird, you should use the
[Lightning](https://addons.thunderbird.net/de/thunderbird/addon/lightning/)
plugin.

# borg

We already know how to secure our data and protect it from being
accessed by unauthorized persons. But we also have to secure the data
from possible crashes and degrading of hard disks in time. There are
many different programs, which can handle backing up your data in
Linux. I would recommend the usage of
[borg](https://borgbackup.readthedocs.io/en/stable/#). It provides a
comprehensive documentation, a convenient command line interface, and
some very attractive features. Most importantly it produces
dedublicated backups, meaning if you backup one set of folders and
then backup a second one all data shared between these two sets will
just be linked and not stored twice. This is very convenient when
having different machines with similar setups or when performing
(automated) snapshots.

First, we have to initialized the repository, which will contain all
archives and data. I call it *borg_backups* in here but you can, of
course, pick a different name.

{{< highlight bash >}}
borg init --info -e repokey --show-rc borg_backups
{{< / highlight >}}

The `-e repokey` option enables encryption for the backup and asks you
for a passphrase to protect it. 

The following command now creates an archive called *archive1* in
*borg_backups*, which will contain your *Documents* folder but
excludes all folders called 'Download' and 'tmp'.

{{< highlight bash >}}
borg create --info --show-rc --exclude-if-present tmp/ --exclude-if-present Download/ --progress --list -x --compression lzma borg_backups::archive1 ~/Documents
{{< / highlight >}}

The `--compression zlma` option specifies the level of compression of
the backup, `-x` excludes all file systems mounted into the specified
folder tree, and `--info --show-rc --list --progress` provide you with
detailed information what borg is doing and if it works properly. You
can list all archives in a repository using the `borg list` and mount
them using the `borg mount` command. Quite intuitive, isn't it?

# pass

Last but not least, what do we do about our passwords? It is
advised to have very long and complicated passwords and to never use
the same password for web pages or accounts, which are topically
related. But how to keep track of and remember all these passwords and
how to come up with safe ones? For all of these problems
[pass](https://www.passwordstore.org/) will be our command line
solution of choice.

Firstly, we have to initialize the password store using our personal
GPG key. This means the whole store is encrypted using GPG and only
you can read it. Even more reason to remember your passphrase and to
backup your private key.

{{< highlight bash >}}
pass init <KEY_ID>
{{< / highlight >}}

Now, we can both insert existing passwords into the store and create
new and safe ones from scratch.

{{< highlight bash >}}
## Insert a password
pass insert Email/provider1
## Generate a password
pass generate Email/provider2 48 -n
{{< / highlight >}}

It's good practice to keep a structure like 'Email/provider1' and
'Account/service3' (with 'provider1' and 'service3' being replaced by
the actual names, of course ;)) since it introduces a tree-like
structure in your password store, which is more easy to navigate. The
generated key will be 48 characters long, completely random, and due
to the `-n` option only consisting of ASCII symbols. But such random
passwords are only applicable if you solely access the associated
account via a computer containing or at least having access to your
password store. You certainly do not want to memorise those monsters.

To list all available passwords and access a specific one password,
you can use the commands below.

{{< highlight bash >}}
## List all stored passwords
pass list
## Access a particular password
pass show Email/provider
{{< / highlight >}}
