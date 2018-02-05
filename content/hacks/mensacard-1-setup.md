+++
date = "2017-01-09T20:02:00+01:00"
draft = false
comment = true
type = "post"
slug = "mensacard-pt-i"
title = "Mensacard Hack Pt. I"
subtitle = "Setup"
tags = ["Apache Cordova", "NFC"]
description = "In this little series I will try to duplicate the RFID based card of my local cafeteria. On the way I will explore many nice tools and frameworks like the Android SDK, Apache Cordova, JavaScript based app programming, NFC/RFID technology, databases, and maybe even some Arduino related stuff."

+++


In the first part of the series we will do the whole setup for the
development environment and all the code is available on my [Github
page](https://github.com/theGreatWhiteShark/mensacard-hack).

# Introduction

Since NFC and RFID is imho a quite nice way to communicate with things
you build which do not have any power supply I wanted to dive into
this topic since quite a while. And be honest: who wants to have
cables all over the place when you also could do it without? 

A second thing which also bothered me was the security issue of RFID
chips in all sorts of cards you have in your wallet. While I was
buying a RFID block-condom for my ID at the 33C3 a guy from
[digitalcourage](https://www.digitalcourage.de/themen/rfid) told me a
lot of credit cards actually provide the credit card number as well as
the date of expiration without any encryption. Creepy. So I decided
(based on an idea by old [Justus](https://github.com/jusjusjus)) to
check out the card of the cafeteria of the local university I'm doing
my PhD at. 

![cafeteria card](/thegreatwhiteshark.coding.io/images/posts/2017/mensa-card-hack/cafeteria-card.jpg)

## Roadmap
1. Firstly, we will do the setup for the Apache Cordova development
   environment. 
2. Then I write some code to extract the content of the cafeteria card.
3. I will write this content to a NFC-tag and see if I'm able to pay
   with it in the cafeteria.
4. Last I will check out how easy it is to read out the content of the
   card
   with a NFC-enabled Android phone. So, is it enough to stand next to
   someone while she has the card in her wallet? How large is
   the distant allowed to be or are the clothes and the wallet already
   protection enough?
   

## Requirements
- I will use a Huawei ALE-L21 phone running Android 6 and a Linux
  laptop running Ubuntu 16.10 
- To build an app to read the card's content we will use
  [Apache
  Cordova](https://cordova.apache.org/docs/en/latest/guide/cli/index.html)
  as the underlying framework and the
  [Android debugging bridge
  (ADB)](https://developer.android.com/studio/command-line/adb.html)
  in order to install it. 
- In addition I will use an Arduino Mega with a NPX PN532 NFC shield
  to write 
  the content to the tag, since I feel the urge to using my Arduino and
  mini computers more frequently. :) 

## Reference 
For everyone how want to dive deeper into the subject of NFC I can
recommend the
[Beginning NFC](https://www.amazon.com/Beginning-NFC-Communication-Arduino-PhoneGap/dp/1449372066/ref=sr_1_1?s=office-products&ie=UTF8&qid=1483802943&sr=8-1&keywords=igoe+nfc) by
Tom Igoe, Don Coleman and Brian Jepson. Its very applied and you find
a lot of actual code in there. Clearly intended for makers and
hardware enthusiasts. 

# Setting up the Android environment
Before we start building our own custom app we have to set up the
Android development environment. 

Firstly, we have to set up the Android SDK (software development kit). A
year ago I would had suggest to go to the
[Android web page](https://developer.android.com/studio/index.html)
and download the newest kit. But it seems to be not available anymore
separately from Android Studio. Which sucks. Also I'm *not* using
Android Studio but Emacs for developing. 

Fortunately the SDK is included in the Ubuntu package repositories so
we don't have to think about setting the whole thing up and updating
at all. 
```bash
sudo apt install android-sdk android-sdk-build-tools  android-sdk-platform-tools
```

To check if the setup was successful connect your Android phone via
USB go to the *Settings > Developer options* and enable *USB
debugging*. If you don't see the *Developer options* go to *Setting >
About phone* and click about eight times on the *Build number*. Then
check if you can see your phone via the *Android debugging bridge
(ADB)* 

```bash
adb devices
```

Now we have to install all the headers etc. to be able to build an app
for our Android 6. 

When I programmed some Cordova apps a year ago I installed the SDK
manually and used the **android** tool to install the individual
platforms. So you might think we already have all the tools installed
via the packages from above. But unfortunately they are not
included. So I headed back to the
[Android developer
page](https://developer.android.com/reference/packages.html) to
download the SDK manually. But guess what. Google stopped supplying 
them but we can still download the SDK-tools (which we will use to
download the Android SDK) at the very bottom of
this [page](https://developer.android.com/studio/index.html). Be sure
the folder is actually called *"tools"*! If not the **android**
program for downloading the platforms won't work (just cost me about
a hour). 

```bash
wget https://dl.google.com/android/repository/tools_r25.2.3-linux.zip
unzip tools_r25.2.3-linux.zip
mkdir --parents ~/software/android
mv tools ~/software/android-sdk
~/software/android-sdk/tools/android
```
Then select Android 6.0 (API 23), Android SDK tools, Android
Platform-tools and Android SDK Build-tools and press *Install
packages*. 

# Setting up Apache Cordova

To install Apache Cordova just use the node.js package manager

```bash
sudo npm install -g cordova
```

So Android is already set up as our development platform and we have
to check what is still missing to deploy our app.

```bash
cd cordova
cordova platform add android
cordova requirements
```

Alright. So now we have to tell Cordova where to find all the
installed libraries and download
the
[Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). 

Therefore we add the Android SDK tools to our search *PATH* and set a
*ANDROID_HOME* and *JAVA_PATH* variable.

```bash
echo 'export PATH=$PATH:$HOME/software/android-sdk/tools:$HOME/software/android-sdk/platform-tools' >> .bashrc
echo 'export ANDROID_HOME=$HOME/software/android' >> .bashrc
# be sure to replace the PATH_TO_DOWNLOADED_JDK with the appropriate path
echo "export JAVA_HOME=PATH_TO_DOWNLOADED_JDK" >> .bashrc
```

(Setting up the Java environment actually gave me a huge amount of
trouble. Firstly, I wanted to suggest you to install the *default-jdk*
*default-jre* and *icedtea-plugin* packages. But after setting the
*JAVA_PATH* to /usr/lib/jvm/openjdk-9-jdk-amd64 (or openjdk-8- or
whatsoever) Cordova couldn't find the Java environment and constantly
complained **cordova Error: Requirements check failed for JDK 1.8 or
greater**. Downloading the JDK from the link mentioned earlier and
setting it up also didn't resolved this issue. Only after I removed
all JDK related packages from Ubuntu Cordova was finally able to find
and use the downloaded JDK. There had to be something in the
background of the Ubuntu system masking the Java paths. Re-installing
all the programs which were removed upon the JDK removal (like
tuxguitar) did not broke the Cordova setting again. So better try this
approach since all sort of different advises on stackoverflow didn't
worked for me.) 

## Installing the app
Now that everything is set up we just need to enter the following
command while our Android phone is connect via USB and its
USB-debugging is enable and the Mensacard app is getting installed. 

```bash
cordova run android
```

NOTE: If you for some reason had to remove and add the android
platform for Cordova you will loose the custom icon of the
app. Unfortunately I was not able to link them via Cordova's
*config.xml*. Instead just replace the icons in
platform/android/res/mipmap-* with the ones in www/res/android. 

Alright. So let's activate the NFC of the smartphone and let's check
if its able to read the cards information. It works! 

![it works](/thegreatwhiteshark.coding.io/images/posts/2017/mensa-card-hack/cordova_works.png)

# Outlook

Awesome. This concludes the first part of this little series.

[Next time](mensacard-pt-ii), we will tweak the very simplistic first
draft of the JavaScript app to actually fulfill our needs of storing
and writing the content of the card. 

So see you next time!
