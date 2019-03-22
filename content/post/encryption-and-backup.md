+++
date = "2019-03-17T13:49:50+01:00"
draft = false
comment = true
type = "post"
slug = "encryption-and-backup"
title = "Encryption, password management, and backups in Linux"
subtitle = "An easy introduction to some of the most important tools and concepts"
tags = [ "Linux", "data" ]
description = "Be it malicious crackers, evil governments, or all-devouring US companies, we have to protect our data from their greedy grasps. But in the beginning it seems to be such a hard task. There are tons of tools and one does neither want to spend several weeks on reading documentation nor to use insecure ones. To alleviate this pain, I will give a short introduction by presenting four simple but essential tools: **dm-crypt/LUKS** for hard disk encryption, **GnuPG** for encrypting individual files, emails etc., **borg** to securely and efficiently backup your data, and **pass** to manage and store your passwords."
+++

Since at least the revelations of Edward Snowden we know that
encryption is not just an important but also a quite pressing
issue. But, unfortunately, there is no one size fits all algorithm
that works with all kinds of data and communication protocols. We are
therefore bound to at least peek into different topics of this rather
complex field. But don't be afraid. I wrote this introduction with
people at a beginner level in Linux in mind and won't touch a single
bit of mathematical theory on encryption in here.

Most of the tools presented here work independent of each other except
of the *pass* program, which depends on a proper *GnuPG* setup. For
the remaining parts you don't have to read the post as a whole but can
only pick those topics you are most interested in. We will start with
the encryption of entire disks using
[dm-crypt/LUKS](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt). Then
a guide to set up the [GnuPG](https://gnupg.org/) either from scratch
or by importing an existing key is presented in addition with some
remarks of how to use it for encrypting files and emails. The same
encryption mechanism will be also used when backing up your system
using [borg](https://borgbackup.readthedocs.io/en/stable/) and storing
and managing your passwords using
[pass](https://www.passwordstore.org/), which both will be explained
in the two remaining sections.

# dm-crypt/LUKS encryption

Whether you hard disk or laptop gets stolen, you have to save personal
data under the jurisdiction of the [General Data Protection
Regulation](https://en.wikipedia.org/wiki/General_Data_Protection_Regulation)
(in Europe), or there is a policy search and massive seizures in your
local hackspace or at home, you don't want your data to be accessed by
unauthorized persons. But since you might use these devices on a daily
basis and maybe even in combination with different platforms, you want
the tools to be both convenient and independent of a particular
operation system. 

This is where
[dm-crypt/LUKS](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt)
and its Linux frontend `cryptsetup` comes into play. There is an
implementation for Windows systems, although I never tried it, and it
allows you to access and use your hard disk as usual after providing
your password once per startup independent on your particular choice
of file system of that disk. It is also used when you decide to encrypt
your home folder during the installation of Linux.

To encrypt a disk using LUKS, we will use the following command in a
bash shell. Note that you have to exchange the */dev/sdX* with your
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

It will use the device mapper to allow access to the encrypted device
under the path */dev/mapper/label*. All data read from or written to
this location will be de- or encrypted on the fly. So, accessing such
an encrypted device is bound to be both slower and to consume more
energy.

If you already set up a file system on the device, jump right to the
mounting of the device and use it like an unencrypted one. If you, on
the other hand, just used the `cryptsetup luksFormat` command, the
device has no file system and we have to create one. Which one is
totally up to you. You can either create the default
[ext4](https://en.wikipedia.org/wiki/Ext4) one using `mkfs.ext4` or a
more advanced one like [btrfs](https://en.wikipedia.org/wiki/Btrfs)
with `mkfs.btrfs`. Optionally, you can override all existing data
remaining on the disk (hidden and inaccessible).

{{< highlight Bash >}}
sudo apt install dirmngr
sudo apt-mark auto gpgsm dirmngr
{{< / highlight >}}
 
 *dirmngr* takes care of talking to the GPG keyserver since version
 2.1 All the examples you can find in the internet using the
 *--keyserver* argument to `gpg` are obsolete.
 
 `apt-mark` is a unified interface to set the properties of installed
 package. Using the *auto* command, they are handled as installed
 automatically and will be thus issued for the autoremoval as soon as
 no other package is depending on them anymore.

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

Now we know how to encrypt the whole disk with all data it
contains. But as soon as we use it the data is not encrypted anymore.
Also when we either want to transmit the data via a public network or
upload it to some sort of server we definitely want it to be
protected. But how to do it? One very prominent way is the asymmetric
encryption using GPG. Asymmetric, because each party has both a public
key it has to distribute to all persons it want to share the data with
and a secret key no one else should ever have access to. Using the
public key of another person a file can be encrypted in such a way it
can only be decrypted using the corresponding secret key. By using
your own public key, you can, of course, encrypt a file in such a way
only you can access its content. This way you can upload sensitive and
confidential data to servers like e.g. GitLab. But first, we have to
generate a key if you do not have one yet.
 
### Generate key

We will do so using the command below. If you have `gpg2` installed,
don't hesitate using it instead.

{{< highlight Bash >}}
gpg --full-gen-key
{{< / highlight >}}

You will be asked a couple of questions. If you have no experience,
just choose 
=======
```

Now you can mount the device and use it as usual.

``` bash
sudo mount /dev/mapper/label /mnt
```

In case you plan to always decrypt the device using the same label,
you can also create a corresponding entry in the *bottom* (since the
file it getting parsed from top to bottom) of your */etc/fstab*. This
allows more easy mounting using particular permission. E.g. the
following line allows a user to mount and unmount the device without
`sudo`, to execute binaries located on it, and provides her read and
write access. 

```
## Be sure that the /media/label folder does exist
/dev/mapper/label /media/label btrfs rw,exec,noauto,user,async,dev,suid 0 2
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
 
### Generate key

``` bash
gpg2 --full-gen-key
```

Choose 

> Please select what kind of key you want:
> (1) RSA and RSA (default)

a keysize of 4096

> What keysize do you want? (3072)
> 4096

and expiration date in the near future.

> Please specify how long the key should be valid.
> 0

Enter email address, your real name, and a comment.

Next, pick a secure passphrase (long! but also big and small letters
and punctuation signs). Since we will use this passphrase to access
our password store, using `pass generate`, which will be covered
later, unfortunately is off the table. So set one you will still
remember in a year.
 
### Publish key

In order for others encrypt data, like emails, with your public key,
they, of course, have to get a hold of it first. A common thing to do
is to upload it to a GPG keyserver. The command handling it does live
outside of the GPG package nowadays and is not installed per default
on most devices. 
 
{{< highlight Bash >}}
sudo apt install dirmngr
sudo apt-mark auto gpgsm dirmngr
{{< / highlight >}}
 
`dirmngr` takes care of talking to the GPG keyserver since version
2.1 All the examples you can find in the internet using the
*--keyserver* argument to `gpg` are obsolete.
 
`apt-mark` is a unified interface to set the properties of installed
package. Using the *auto* command, they are handled as installed
automatically and will be thus issued for the autoremoval as soon as
no other package is depending on them anymore.

Now, the following command will upload the public key associated with
the key ID \<KEY_ID>. You can find the ID by running `gpg --list-keys`.

{{< highlight Bash >}}
gpg --send-keys <KEY_ID>
{{< / highlight >}}

### Create a revokation certificate.

But what if you loose your secret key or the corresponding passphrase?
People will use your public key from one of the key servers and keep
sending you encrypted files and email you are not able to open
anymore. To get your key removed from the server, you need a so-called
revokation certificate. It's best practice to generate it right away
and store it on all your private hard disks and backups.

{{< highlight Bash >}}
gpg --output revoke.asc --gen-revoke <KEY_ID>
{{< / highlight >}}
 
### Export and import

Instead, you can also import your key generated on another device. But
be sure to [shred](https://linux.die.net/man/1/shred) any intermediate
copies of your private key.

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

For further information check out the following [link](https://easyengine.io/tutorials/linux/gpg-keys).

### Encrypt and decrypt a single file

Now, let's use our private key to encrypt a file just for ourselves.

{{< highlight bash >}}
## Encrypting a file
gpg --encrypt -a --recipient <KEY_ID> file
## Decrypting a file                               
gpg --decrypt --output file file.asc
{{< / highlight >}}

When en- and decrypting email using Thunderbird, you should use the
[Lightning](https://addons.thunderbird.net/de/thunderbird/addon/lightning/) plugin.

# borg

We already know how to secure our data and protect it from being
accessed by unauthorized persons. But we also have to secure the data
from possible crashes and degrading of hard disks in time. There are
many different programs, which can handle backing up your data in
Linux. I would recommend the usage of
[borg](https://borgbackup.readthedocs.io/en/stable/#).

Creates a folder called *borg_backups*, which will contain all
archives.

{{< highlight bash >}}
borg init --info -e repokey --show-rc borg_backups
{{< / highlight >}}

{{< highlight bash >}}
borg create --info --show-rc --exclude-if-present tmp/ --exclude-if-present Download/ --progress --list -x --compression lzma --dry-run borg_backups::archive1 ~/Documents
{{< / highlight >}}

Creates an archive called *archive1* in *borg_backups*, which will contain your *Documents* folder.

The archive will consume almost no disk space for files or parts of
files that have already been stored in other archives. Therefore, why not putting the folders in different archives?

{{< highlight bash >}}
borg create --show-rc --progress --compression lzma --list --info borg_backups::dotedAbyzou ~/.[A-Z]* --exclude ~/.anaconda3 --exclude ~/.cache --exclude ~/.ccache --exclude ~/.npm --exclude ~/.go1.4 --exclude ~/.go1.11
{{< / highlight >}}

# pass

Generate safe passwords

{{< highlight bash >}}
pass generate Email/provider 48 -n
{{< / highlight >}}

Access the password
{{< highlight bash >}}
pass show Email/provider
{{< / highlight >}}
