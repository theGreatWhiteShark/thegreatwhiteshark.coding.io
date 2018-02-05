+++
date = "2017-01-29T20:02:00+01:00"
draft = false
comment = true
type = "post"
slug = "mensacard-pt-ii"
title = "Mensacard Hack Pt. II"
subtitle = "Ionic"
tags = ["Apache Cordova", "NFC"]
description = "In this post, unfortunately, I will not be able to tell you more exciting stuff about either the card itself or the app reading/writing it. Instead, I will provide you with some insights into the Cordova-based framework **Ionic** and why *I recommend you to NOT use it*. This may sounds quite boring, but this is how stories about the fugacity of software frameworks are." 

+++

# Why I tried using Ionic.

The main reason for me to do this project is to acquire all necessary
skills to quickly prototype an Android app.

It happened a couple of times already that I had a very nice idea or
even a more or less 
elaborate roadmap to a product but in the end decided to file it away
due to the gigantic overhead of app development. I really dislike
doing this. It feels like throwing away so much potential while
heading towards a future where one is employed in a faceless company
programming/calculating some redundant and useless stuff. I know this
is a way to bleak picture, but you get the point: One would miss a lot
of fun.

While **Cordova** itself is a nice tool its plain JavaScript and HTML
output is neither really the most beautiful nor sophisticated one. For
experienced Web 
developers this most probably would not cause any problems at all, but
I would 
have a hard time making the whole thing responsive and look
beautiful. But there is a cure: by using a framework on top of Cordova
one gets a better look in exchange for a more complex system and
since I did not intended to dive to deep into the topic for this
project it would be the best choice for me.

When you scroll down the [web page](https://cordova.apache.org) of
Cordova you see a list of all the different frameworks building on top
of it, like Ionic, Monaca, Onsen UI, and Taco. After review their
capabilities, licenses, and user base I decided to go
for [Ionic](https://ionicframework.com/).

**Ionic** offers the following advantages over **Cordova**:

- It is based on
  [AnuglarJS](https://angular.io/docs/js/latest/quickstart.html). On
  the one hand, this is quite a huge overhead, since Angular is a rather
  complex framework itself and
  uses [TypeScript](https://en.wikipedia.org/wiki/TypeScript) (not
  JavaScript), on the other hand, it provides comprehensive tutorials
  and references, has a very big user base (which already answered
  quite a number of questions on StackOverflow), and produces
  state-of-the-art visualization.
- It provides a large number of HTML 
  [components](https://ionicframework.com/docs/v2/components/#overview) 
  allowing the user to have a native feeling she knows from other
  Android applications out of the box.
- Its developer tools are more advanced. Instead of building and
  running your app every single time, you can deploy it in your
  browser or smartphone using 
  `ionic serve` and whenever you save a change of any of its source
  files, the app is going to be recompiled and reloaded. Just like you
  know from nodejs. (Ionic itself is a node package)

# Why I failed.

Ionic itself does not host a tutorial of how to get started with its
app development. But using the [road to
heros](https://angular.io/docs/ts/latest/quickstart.html) tutorial of
Angular  while cross-referencing to the Ionic 
[component's
documentation](https://ionicframework.com/docs/v2/components/#overview)
got me (a noob in both JavaScript and web/app development)
started. Also check out these two videos
on [Angular2](https://www.youtube.com/watch?v=-zW1zHqsdyc) and
[Ionic2](https://www.youtube.com/watch?v=ilM8YorL_jI). 

Already at this first steps I noticed something unsettling: most of
the advises that can be found on StackOverflow etc. do not work
anymore. Ionic had an upgrade to **Ionic2** in 2016 which is now not
based on AngularJS anymore but instead on **Angular2**. While this is
[said](http://blog.ionic.io/angular-2-series-components/) to be a
benefit for all developers it inevitably breaks all the code of the
previous version. So in the end I felt like an early adapter in a
language I am not familiar with. Hmmm. In addition to this
inconsistency, it is also possible to power a Ionic2 app with either
JavaScript or TypeScript code. While it was probably intended to grant
the user more freedom, it is just highly confusing for novices, since
half of the solutions you manage to find online you do not know
how to apply them. Because it's not just the language, but also the
structure of the app that changes, it is very easy to get lost on the
way. 

## Databases

A feature I was quite excited about was the possibility to use
databases within the app. After all 'big data' is a buzzword which
should appear in every application nowadays.

According to the
[documentation](https://cordova.apache.org/docs/en/latest/cordova/storage/storage.html)
of Cordova there are four ways to store your data (persistently):
- *localStorage* which is quite easy to handle but limited to
  key-value pairs only 
- *webSQL* which was the SQL implementation in browsers but is now
  [deprecated](https://en.wikipedia.org/wiki/Web_SQL_Database) 
- *IndexDB* which is recommended by the Cordova community. In addition
  it is also well
  [documented](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Basic_Concepts_Behind_IndexedDB). Be
  sure to check out the IndexDB documentation for all kind of 
  information regarding non-SQL databases. It is quite well written.
- additional plugins like *SQLite*.

Worth mentioning you can also
use [localForage](https://github.com/localForage/localForage) which is
some sort of frontend using the first three ways of Cordova's storage
capabilities, Angular-based wrappers
like [Angular-cache](http://www.pseudobry.com/angular-cache/) (which
doesn't seem to be developed anymore)
and [ngStorage](https://github.com/gsklee/ngStorage) and
databases-as-a-service like [Firebase](https://firebase.google.com/)
or [Backand](https://devdactic.com/ionic-backend-database/).

I decided to work with *IndexDB* but after some search in the web I
had to realize that there is neither an example nor an explanation how
to use it with Ionic. Instead to most recent versions of blog posts
and YouTube videos all rely on the *SQLite* plugin of Cordova.

But regardless which these I tried, I failed.

After some sleepless nights I found the problem to be not my inferior
programming skills but the developers of Ionic2, who
again [broke](https://github.com/driftyco/ionic/issues/8269) the
existing code. They moved the modules responsible for storing data and
broke all apps relying on them. Also the interface for accessing
databases changed. Apparently. 

Most probably, one can still access
databases, but I have no idea how. There are some snippets
of [code](https://ionicframework.com/docs/v2/storage/)
and
[information](https://github.com/driftyco/ionic/issues/8269#issuecomment-250590367) around,
but no working example. Without any context, this is not of that
much help for noobs like me.

It is one thing to make an upgrade of you language/framework and to
break the code of the previous version. But to break the code twice a
year is just to much.

As a conclusion I can just recommend to **NOT** use **Ionic2**, unless
you are already familiar with coding in Angular2 and TypeScript as
well as with the peculiarities of Cordova. In addition, even the
example application need a lot of time for startup.

# What will happen next

For my next projects I will not use **Cordova** again, but instead will
develop apps in plain **Java**. In the beginning I thought there will
be less overhead and an easier development. But even without the
direct comparison to Java, I can conclude that this was definitely not
the case. It's a fast developing framework used by only a few
people. In addition, you have to have a Mac to deploy your apps on iOS
and it's 
way slower than a native application. 

For this series I will nevertheless stick to Cordova and will build an
application to read and write the Mensacards content in the [third
part](mensacard-pt-iii). The Ionic version, however, I will definitely drop.
