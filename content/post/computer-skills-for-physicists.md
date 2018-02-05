+++
date = "2017-02-02T20:02:00+01:00"
draft = false
comment = true
type = "post"
slug = "how-much-computer-skills"
title = "How much computer skills does a physicist need?"
tags = ["R"]

+++

Computational skills are something almost every scientist has to offer
nowadays. But unfortunately the least of us are actually trained in
it. So most colleges (including me) took the autodidactic approach, a
long road full of detours and wasted effort.

For those of you who are already decent programmers but did not dived
into a lot of languages and tools, I try to compile a summary of all
the essential stuff I tried during my PhD in this post. I will write
about which language IMHO is the best fitting for a scientist and
which tools can improve and fasten our workflow.

Since I'm a busy man, there are two very important topics I will not
cover in this post:

1. [Best practice of programming](http://journals.plos.org/plosbiology/article?id=10.1371/journal.pbio.1001745)
2. [Security](https://ssd.eff.org/) and [privacy](https://prism-break.org/en/)

Maybe I do some posts about it in the future. Don't know. Meanwhile
check out the links!

# Introduction

Every now and then I am asked by some colleges what kind of software
or tools I am using and if I could recommend some of them. Since it
was not long ago that I started to dive deeper in all sorts of
different aspects of computer hardware and software, I totally get
their point: There is a huge pool of different options out there and
especially beginners are almost crushed by the vastness of available
tools.

So usually you end up using the tools your colleges are most familiar
with. But are these really the ones fitting you best? In fact I also
started writing some Matlab code in the beginning of my PhD. Then I
want to get rid of all proprietary software, so I rewrote everything
in [Octave](https://www.gnu.org/software/octave/). But after a while I
realized this language was just not powerful enough for me and then I
finally switched to [R](https://www.r-project.org/) what I continued
using up to now.

I am aware this search is a hard one. I mean basically all this effort
of porting the my code was a waste of time and one has more than
enough work to get done. So I decided to give a talk and write this
post regarding this topic. Maybe it will help someone of you finding
her/his way.

In the first part I will review which language does fit a scientist
the most and in the second I will mention a lot of tools which can
improve your productivity a lot. In addition I will also review the
basics of web development along with some popular frameworks. This is
a great way for making prototypes, which are fast to share and easy to
use by others. To sum up, I will also talk about the future
perspectives of our current languages and workflows.

# Which language should I use?
### A plea against monolingualism
One of the most widespread misconceptions about computer languages is
that they are hard to learn. But this is not true at all. Well, it is
hard to learn programming. Yes. But as soon as you grasp all the
concepts like function and variable definitions, looping and
conditions, local and global scoping, object orientation, commenting,
and debugging you are almost done. The specific implementation, the
syntax and the amount of available features might differ from one
language to another, but this difference is mostly a semantic one. As
soon as you are able to write a function, to e.g. sum two variable,
you could do the same in most high level languages as well within a
hour.

Of course I'm just talking about simple task right know and it takes
quite some time to *really* become familiar with a computer
language. But always keep in mind:

- Don't try to solve every problem in one single language.
- Don't be afraid of using other languages for simple tasks.

It's getting simpler with every new language you try and your set of
skills will increase dramatically.

### Requirements for our language of choice
That being said, I would strongly advice you to have a main language
which you are familiar with. But which one to choose?

Most of the time you address this question, the answer would be an
instructive, maybe even slightly musing, "Depends...". Well, of course
it depends on what you want to do. But we are scientists, right? So
even if the task at hand differs between the individual branches and
fields, there are certain basic requirements we all share, which in
the end lead us to a very limited number of languages.

We need:

1. Fast prototyping
2. A general purpose language
3. Tons of libraries
4. Beautiful plots
5. Free and open-source software
6. The possibility to enhance the speed of our program significantly

### Fast prototyping
The most important requirement IMHO is that the language allows you to
get your job done as fast as possible. So when you find some nice
article you want to reproduce, you want to implement an idea you came
up with during a talk or a discussion, or your supervisor asks you to
check for something: you want to get it done in no time.

That's why I highly recommend to use a high-level language you do not
have to compile. Debugging just takes longer with all the compiling
and linking. It does not matter how skilled you are. Having an
interactive shell is always an advantage. Plus in languages like *R*,
*Python*, *Lua* etc. you have to type less characters to get the job
done (compared to e.g. *C++* or *Fortran*).

### A general purpose language and tons of libraries
To put it simply: you don't know which task are waiting ahead. Maybe
you have to use a [batch
system](https://en.wikipedia.org/wiki/Batch_processing) and parallel
programming, or you will have a collaboration providing you with
[GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) or
[HDF5](https://en.wikipedia.org/wiki/Hierarchical_Data_Format)
files. Who knows? But in any case: it is best to be prepared.

I already told you, you shouldn't be afraid of solving simple tasks in
other languages. Some problems are way more easily solved in their
native way. But it is something completely different if you **have**
to use other languages, because your main one is just not powerful
enough. So do not choose for example *Octave*. Its scope is too
limited.

### Beautiful plots
This point might seem somewhat trivial. But have you ever harnessed
the full power and beauty of interactive [D3js](https://d3js.org/)
graphics? Don't worry, I will not advice you to pick JavaScript as
your main language (although it's one of the most important for
smaller tasks), but rather to take a look at the
[wrappers](http://www.htmlwidgets.org/showcase_leaflet.html) around
it.

### Free and open-source
Well, every argument supporting
[open-access](https://www.eff.org/issues/open-access) in journals or
[open-data](https://netzpolitik.org/tag/open-data/) basically also
holds for open-source. You got the benefit (in both code and support)
of a community, you can easily share your code and you can be part of
something bigger.

So don't be a [dick](https://en.wikipedia.org/wiki/Steve_Ballmer) and
support [free and open-source software](https://fsfe.org/).

### The possibility to enhance the speed of our program significantly
In a lot disciplines, you have to deal with problems/code which takes
for hours, weeks or even months to run. There it is most important to
be able to highly optimize your program. But this does not mean you
have to completely switch to *C++* or even *Assembler*. You can
combine the fast prototyping of high-level languages with the speed of
lower ones. I will cover this point in the next section.

### The choice
![choice](/thegreatwhiteshark.coding.io/images/posts/2017/how-much-computer-skills/slide-choice.png)

After meeting all the requirements, our candidate list has boiled done
to **R** and **Python** (starting with more than 30 languages I tried
so far). So which one of those two is fitting your needs?

#### Python or R?
If you have to handle measurements, work with time series or dynamical
models, or if you just have to do some statistics in general, I highly
recommend using *R* over *Python*.

If you on the other hand do not do statistics at all, than use
*Python* over *R*.

Okay. Why? Since *R* is developed and maintained by statisticians and
the majority of the user community are actual data scientists or
statisticians. So the core of the language is devoted to data analysis
and the target audience are (natural) scientists. To get started,
check out the [books and packages](http://hadley.nz/) of Hadley
Wickham.

*Python* is developed, maintained, and mainly used by computer
scientists. There are a bunch of nice packages for scientific
computing, but not in its core. And it is always best to rely as much
as possible on a languages core, or else you will end up with package
updates breaking your code, or code requiring different versions of
one and the same package. You know
[Anaconda](https://www.continuum.io/downloads)? Its a quick ease of
the symptoms, but no cure for the disease. In *R* this is more smooth,
but nevertheless even there you will run in such problems sooner or
later.

But keep in mind, I don't want to participate in any language-war (we
will come to editors at a later point ;) ). *Python*, as well as *R*,
is a nice languages which has their flaws and benefits. *R* is just
more superior for analyzing data, whereas python is more general.

Since I develop my code in *R* and just occasionally use *Python*, I
will provide references mostly for the former one. But don't worry. In
most cases there is a *Python* counterpart
available. [Duckduckgo](https://duckduckgo.com/) is your friend.

#### Combine them with C++
Whenever your code is becoming pretty slow, in most cases it's the
work of just one or two functions in your program. The best way to
deal with this problem is to identify these functions via
[profiling](http://adv-r.had.co.nz/Profiling.html) and to rewrite them
in **C++** (which runs way more faster). But you do not have to handle
memory allocation, compiling, linking and the integration of the code
into *R* or *Python* yourself. There are some really neat
[packages](http://rcpp.org/) handling all of this for you. Since all
the compiling and linking is handled in run-time by your main
language, it feels almost like interactively handling your *C++*
code. Which is awesome!

Of course, if your program would run for months, than you have to
write it native in *C++* and come up if some intelligent memory
handling. But this is rarely necessary.

Another way to speed up your calculations is of course to use multiple
[threads](https://en.wikipedia.org/wiki/Thread_(computing)). Per
default all programming languages will just use one core of your
machine. So if you want them to use more resources (e.g. whenever you
have independent for loops), just
[tell](http://adv-r.had.co.nz/Profiling.html#parallelise) them. In
addition there are a lot of
[guides](http://www.burns-stat.com/pages/Tutor/R_inferno.pdf) for
making your code run faster.

#### Working with GPUs
Sometimes even a cluster of CPUs is not enough and the only thing
getting your code done in a decent amount of time is the graphic
card. With [Cuda](https://www.nvidia.com/object/cuda_home_new.html)
and [OpenCL](https://www.khronos.org/opencl/) you have two *C*-like
frameworks enabling you to harness the power of a modern GPU. But be
warned: Don't do an one-to-one port of your code to one of those
languages. It most probably won't speed up anything. You have to mind
the concept of [data
parallelism](https://en.wikipedia.org/wiki/Data_parallelism)!

While there is a nice support of GPU computing in a number of [deep
learning](http://www.deeplearningbook.org/) frameworks like
[Tensorflow](https://www.tensorflow.org/),
[Theano](https://github.com/Theano/Theano) or
[Torch](https://github.com/torch/tutorials) (I would recommend the
first one) via the [libcudnn](https://developer.nvidia.com/cudnn), *R*
and *Python* are still lacking a decent general support. There are
some libraries around, but the last time I checked they haven't
reached a sufficient user-friendly level yet. But it will come
someday. Don't worry. GPUs are the future.

# Which tools should I use?

## Linux!
I will not elaborate much on this one. It's a way too broad topic. 

Personally I prefer [Ubuntu](https://www.ubuntu.com/), since it has
the biggest community and most of the problems you encounter are
already solved and explained somewhere on StackOverflow. I also have a
second partition running on [Kali](https://www.kali.org/), which I
wouldn't particular recommend for scientific programming, but I also
like it's [Debian](https://www.debian.org/) core.

But it doesn't really matter. You can also use
[Biebian](http://biebian.sourceforge.net/).

> It may be Justin Bieber Linux (also called Biebian), but it still beats Windows and Mac.

#### Window manager
I use the tiling window manager [i3](https://i3wm.org/), which also
gives me a boost in programming velocity, because you do not have to
rely on your mouse anymore. In contrast to
[awesome](https://awesomewm.org/), you only need about half an hour to
configure and get started with *i3*. It's great, so be sure to check
it out.

If I should instead choose a high-level, mouse-driven window manager,
I would definitely pick [Mate](https://ubuntu-mate.org/). It's a fork
of the good, old Gnome2 and IMHO it's just way more beautiful and
intuitive than Gnome3, KDE, or Unity. In addition it is by no means as
complex as those mentioned. Have you ever tried to customize Unity?
This will give you gray hair for sure!

#### Workflow
For those of you who are not used to work with Linux, I will briefly
explain the underlying concepts.

Most of the components of Linux are designed according to the *Unix
principle*: A program should do one thing and it should do it good. So
after switching (from Windows), you may miss some of those big and
omnipotent programs and you are most probably overwhelmed by the
vastness of all these tiny programs you can use. All the files in
*/bin*, */sbin* and */usr/bin* are actual programs! But using [BASH
programming](http://tldp.org/HOWTO/Bash-Prog-Intro-HOWTO.html), you
can glue all these different tools together and create something very
powerful and customized. (I almost managed to not mention *Emacs*
right here as the most potent program of all. But it felt more weird
every time I read this section.)

*Hint*: One of the important commands in my repertoire is
[sshfs](https://help.ubuntu.com/community/SSHFS). Using it, I mount
the home directory */home/user/* of the cluster of the institute I am
working at to e.g. my local laptop. In this way all my work, every
edit is done in the home of the institute, which is secured by
frequent backups. So if I loose my laptop or it breaks down, I do not
loose any work related stuff.

## The editor

![silde-editor](/thegreatwhiteshark.coding.io/images/posts/2017/how-much-computer-skills/slide-editor.png)

If you want to become a really fast and efficient programmer, it is
absolutely mandatory to write your code in a decent editor. And there
are only two out there: **Emacs** and **Vim**.

So let's assume you voted for *Python* and are using
[Spyder](https://pythonhosted.org/spyder/) or
[Geany](http://www.geany.org/) for editing your code. At home, you
have a couple of projects: One where you build *C* code for an
[Arduino](https://www.arduino.cc/) in its own
[IDE](https://www.arduino.cc/en/Main/Software), and another where you
build *Android* apps in [Android
Studio](https://developer.android.com/studio/index.html). At work you
have to write talks and papers in *LaTeX* using
[TexMaker](http://www.xm1math.net/texmaker/) and whatsoever.

In short: You are going crazy in changing your development environment
every single hour. And every time you are facing different shortcuts
and features. No! Just use a real editor. It will boost your
performance significantly.

#### Emacs or Vim?
How to do this one objectively? Well. So, let's put it this way: If
you are just using computer languages that need compiling, it does not
make that much of a difference which editor you are using. Than it is
essentially just a matter of taste. Either you want to quickly jump
into the editor, change some stuff and head back to the console to run
your code (*Vim*), or you prefer to stay in the editor and do
everything from within (*Emacs*).

But times have changed. Now we do have interactive computer languages
and there it has become an essential benefit to just mark a specific
part of the code in the editor and evaluate it in an shell. Since
Vim's extension system is powered by a scripting language, it does not
have the ability to provide such features. But Emacs does, since it is
relying on *Lisp*. There you are able to have an interactive shell
*within* the editor and to harness all the advantages that come with
it. If you really like the navigation of *Vim* try
[Spacemacs](http://spacemacs.org/).

That being said, there is a disadvantage of *Emacs*: The overhead. To
use it fast and efficiently (not to get started), you need at least a
week. But it's worth the effort! (And while your at it: Check out
[org-mode](http://orgmode.org/). It's like one of the greatest things
ever.)


## Use version control and testing!

As you proceed with your thesis/project, you probably end up writing
more and more code with more and more features. One version after
another. At some point you may want to reproduce a numerical
experiment you did a while ago, or your collaborators finally looked
into the results you sent them ages ago and want you to perform some
additional analysis. But the code you used back then does not really
anymore. Too bad...

Or another example: You talk with some colleges and all of a sudden:
You have a brilliant idea to enhance your code. You immediately get
your hands dirty, redo your code basis and try to apply all the
changes you thought of. But some of them are hard to implement. Some
feature are maybe not even possible to implement, since your computer
language or software framework becomes the limiting factor (happens
faster than you may think). So you get stuck. You try to do some dirty
fixes, but it is not use. In the end you fail and your code does not
work anymore. Too bad. You may have a copy of the old code around, but
some of the new features actually worked and you have to start again
implementing them...

Both scenarios could have been avoided by using version control. There
you can reset your code to any previous state. You can also very
clearly describe all changes you did to your code and you can
implement some new features in a copy of the code, while keeping the
main version working all the time. If it is not just you but a whole
team working on some code, these features become even more desirable.

#### Version control frameworks

![git-slide-1](/thegreatwhiteshark.coding.io/images/posts/2017/how-much-computer-skills/slide-git1.png)
![git-slide-2](/thegreatwhiteshark.coding.io/images/posts/2017/how-much-computer-skills/slide-git2.png)

There are many version control frameworks you can use. I personally used
both [Apache Subversion](https://subversion.apache.org/quick-start)
and [Git](https://try.github.io/levels/1/challenges/1) for quite a
while. But **git** is definitely the one I would recommend to you. For
an introduction into these framework, please refer to the links or the
slides.

#### Version control hosts

Another important question is: Where to host your git repositories?

For most people, I would recommend [Github](https://github.com/). You
get 500MB of storage for a free account and it is the most prominent
web page in this area. But there is one important thing missing you
may need: The possibility of creating a private repository (at least
in the free version). So all the code you put online will be visible
to everyone.

Among others, another option would be
[Bitbucket](https://bitbucket.org/). They hook you up with the
possibility of creating private repositories with a free account.

Personally I have an account at both sides. But those pages can cease
to exist! In the beginning, I had my code at **bettercodes.org**,
which is a blogging web side today. (Quite rarely) I experienced some
downtimes of the **Bitbucket** servers. It was just a matter of
minutes and not dramatically, but annoying. So I would recommend
**Github**.

#### Test your code

[Testing](http://r-pkgs.had.co.nz/tests.html) is one of those things I
just recently added to my code. In the beginning I always thought of
it to be an utter waste of time. I'm doing some testing in the
debugging procedure manually while incrementally improving my
code. So, why I need an additional testing layer on top of it?

Because then you never get fooled around by the same bug twice. 

Whenever you fix a piece of code, you just copy the lines, leading to
the bugs detection, and make a test out of it. Then you just need to
start an automated test (in the command-line or via continuous
integration with e.g [Travis](https://travis-ci.org/)), get a bottle
of mate, and afterwards your code will tell you about its bugs
itself. This makes your package rock-solid and helps you lots.

## Markdown and Pandoc

[Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)
is a super-lightweight markup language, which allows you to quickly
make structured notes and drafts. It features support of tables, code
highlighting and is able to incorporate images and links.

While this would be just a tiny but nice tool for making notes,
combining it with [pandoc](http://pandoc.org/getting-started.html) can
increase the speed of your workflow significantly. Due to this
program, you can convert your *Markdown* documents in many different
formats, including *LaTeX* and *HTML*.

Just write the first draft of your paper, talk etc. in *Markdown*,
convert it to the target MarkUp language and apply some finishing
touches. Ever wrote a table in *LaTeX* or *HTML*? You don't have to
feel this agony ever again.

In addition, it also supports *LaTeX*-style formulas (using
[MathJax](https://github.com/mathjax/MathJax) for *HTML* as well) and
you can use custom templates or those you found in the net to tweak
the output even more.

Just try it!

## Talks in Reveal.js

I already mentioned the possibility to write the draft of your talk in
*Markdown* and to convert it afterwards to save a lot of time. Well, I
was not speaking of *LaTeX* as the target language, but *HTML*.

The reason behind this is simple. *LaTeX* is designed to make highly
reproducible and uniform output (like pages/chapters in books or
different papers in a journal), while *HTML* is meant to create very
individual documents, which should be customizable quite easily (like
web pages ... or talks! ). Did you every try to make all the images of
a *LaTeX* document stick at the positions you really want them to be?
I know, it's possible and I have done it myself. I also customized a
.sty file to make a talk/poster look more appropriate. But believe me,
this is no fun at all!

Instead you should use
[Reveal.js](https://github.com/hakimel/reveal.js). It's a *HTML*,
*CSS* and *JavaScript* based framework, which works just awesome. In
fact it works that well you do not even need any programming skills in
*CSS* and *JavaScript* at all. Just plain *HTML* will produce a
visually state-of-the-art talk.

For those of you who are not familiar with those languages: *HTML* is
the counterpart of *LaTeX*. It contains the skeleton of the document
with headings, bold and italic commands, equations, links etc. and is
more or less agnostic to any style information. *CSS* is the
counterpart of the .sty file in *LaTeX*. It contains all the style
information, like the text font, height and color. But it is way
easier to customize and you should not be afraid at all to
[try](http://www.w3schools.com/css/default.asp) this
yourself. *JavaScript* is used to write the underlying engine making
it concurrent, animated and very flexible. But I definitely do not
recommend you trying to tweak the *Reveal* framework. (unless you
already have a decent amount of knowledge regarding *JavaScript*, *Web
development* using *node.js* and asynchronous function callbacks)!

But when you are heading somewhere to present your talk: Be sure to
have an extra copy of all the **Reveal.js** related files and a PDF
version of your talk at hand. You don't know if you can use your own
computer, the connection to the beamer is working etc.

By the way, the slides you see between this text are made using
*Reveal.js* as well. ;)

#### Benefits

- You get a beautiful and animated presentation for free. This is one
  of the occasions in life where there is really a free lunch.
- You have a precise control over the image positions. Out of the box
  it already looks nice (like in *LaTeX*), but to modify them you just
  have to assign the x and y coordinates in relative or absolute
  coordinates and you are done!
- Since the presentation is powered by a *node.js* server (more on
  this topic later), it runs in your browser and the whole
  presentation is updated as soon as you save one of the underlying
  files. So no more compiling and linking. You do not even have to
  leave your editor for applying changes to your presentation!
- Due to its usage of bootstrap your talks will be rendered nicely on
  a vast amount of different devices, like desktops, laptops, tablets
  and smart phones.

## Knitr | Jupyter

While I was talking about version control you might have thought,
"Okay, sounds reasonable. But I only use small scripts in my line of
work. This whole concept does not really apply to my workflow."

Well, most of the time it's actually the same for me too. Whenever I
do some data analysis, I e.g. fit some models to the data, plot some
statistics and maybe write some functions I most probably won't use at
any future point in time. Version control is not really of much use in
such kind of workflow.

But then again, how to document all your effort so you can present it
to your supervisor, look up some previous results or a patch of an
error you already encountered? If you are using **R**, the answer is
[Knitr](https://yihui.name/knitr/).

You basically just write a mixture of *Markdown* and **R**
code. During compilation the R code is executed and the results (if
you want to even the code itself) are getting rendered, pasted into
the *Markdown*, and the whole thing is getting exported via **pandoc**
to *HTML* or *PDF*.

#### Benefits

- Using this approach, you can just write all your research, all your
  endeavors down like a book. This way you not only document all your
  efforts, but also all your mistakes. I have a folder in my office
  containing the printed PDF of all my previous work. It's just a
  great way to keep track of all the things you have accomplished so
  far.
- *Knitr* (pronounced like neater) will cache chunks of *R* code. So
  it will store the results of each individual code block (when you
  ask it to) and does not evaluate the code, until you change some
  characters in the box. So you do not have to worry about writing
  long documents.
- Since you can also use  the
  [Rmarkdown](http://rmarkdown.rstudio.com/) engine, you are able to
  produce interactive plots and statistics. You have to adjust a lot
  of parameter combinations by hand and reconsider plots created using
  those? Just let some sliders do this work for you! And harness the
  beauty of [D3js](http://www.htmlwidgets.org/).
  
If you are not using *R* but **Python**, you can checkout
[Jupyter](https://jupyter.org/). This is a browser-based framework to
write large documents including code chucks, which also supports
caching and the usage of interactive *JavaScript* magic.

# The web

I already covered some web related tools, but let's dive deeper into
the field of web development. Because it holds some features which can
be incredibly useful for scientists.

- You can use interactive calculations and responsive graphics.
- You can quickly produce prototypes of fully fledged applications.
- It's very easy to distribute your prototypes, since the user is not
  requited to have any knowledge regarding computers (except of
  opening a link in her/his browser). *GTK+*, *Qt* and *Wx*
  applications on the other hand do require quite some software
  expertise  and many people, who never compiled something in their
  whole life or aren't able to keep **Python2.7** apart from
  **Python3**, you just can not reach this way.

## Some basics of web development

![slide-web-basic](/thegreatwhiteshark.coding.io/images/posts/2017/how-much-computer-skills/slide-web-basic.png)

Each web application and web page consists of two parts: A **front
end** and a **back end**. While the *front end* is always written in
*HTML*, *CSS* and *JavaScript* and defines the look and feel of the
user interface/web page, the *back end* is the place where all the
calculations are done, the database look-ups are performed or the
plots are rendered. For this you have a variety of different languages
you can use. Both *front* and *back end* can be in on one computer
(e.g. working in a *Jupyter* notebook), or the *back end* can be run
on a server for everyone in the internet to access using the client
*front end* on her/his end device.

Sounds simple. Doesn't it? Well, it is definitely no magic, but
something quite different from what we scientists are used to
do. Because most of the communication of between server and client
will happen *asynchronously*. This means, when you call a function to
get a value from a server on client-side you don't want to wait until
you receive it. Imagine your browser would freeze, because of some ad
[uBlock
origin](https://addons.mozilla.org/en-US/firefox/addon/ublock-origin/)
failed to remove. It will be the 90's all over again. What a dystopia!
Okay, but what, if this function is supposed to return a value you
need for plotting a function? Well, you have to use a so called
*callback* function instead. To understand this principle of
asynchronism you should [learn you node](https://nodeschool.io/).

## Shiny

But if you just want to have a simple application plotting your
calculations with a bunch of different options and parameters without
any API or database, there is hope for you. Use
[Shiny](https://www.rstudio.com/products/shiny/).

It's a **R** based web framework, which is meant to be used by use
scientists and not by web developers themselves. Therefore it masks
all the asynchronism and the coding in *HTML* and *JavaScript*. The
elements to build your *front end* with and all the code constituting
your *back end* is wrapped in *R* functions. You only need some *CSS*
to customize the view of your application. But this only comes to
during a later point and you are save to skip this step completely.

The greatest thing about *Shiny* is, that you can [get
started](http://shiny.rstudio.com/tutorial/) within a couple of hours!
(Yes, without any prior knowledge about web development, HTML,
JavaScript and whatsoever. But you need some knowledge about coding in
R).

I tried a number of different frameworks for setting up web apps and
*Shiny* is without a doubt the framework of choice for scientists. I
also used it to build [my own
application](https://github.com/theGreatWhiteShark/climex).

But if you want to have an API or plan for specific databases or
encryption, it's not the framework of choice for you. Better use a
more elaborated but complex framework than tweak or outsmart a simpler
one!

## Other frameworks

So, while I'm at it, I will also share the experiences I had with
other frameworks as well.

I already suggested you **node.js**. It is a runtime environment for
*JavaScript*, which comes along with its own package manager
**npm**. It is used at the core a huge number of very nice packages
(including *Reveal.js*). But it is also quite basic and more meant for
setting up a plain FTP or HTTP server than a web app. This would be an
awfully complex task.

To reduce this complexity, a lot of frameworks were build on top of
*node.js*. They have the benefit to be written in
*JavaScript*. Remember: You have to use it for the *front end*
anyway. So why not use it for the *back end* as well? Such frameworks
are e.g. [Meteor](https://www.meteor.com/),
[SailsJS](http://sailsjs.com/), and [Loopback](http://loopback.io/). I
just did some "hello worlds" in them and watched a couple of YouTube
videos, showing the construction of sample apps. Judging based on this
background, I would recommend you to use *SailsJS*.

Apart from a lot of frameworks, written in languages I already
discouraged you to use as your main one, there are of course also some
web frameworks written in **Python**. I personally like
[Flask](http://flask.pocoo.org/) with its
[Jinja](http://jinja.pocoo.org/docs/2.9/) template engine the
most. But [Django](https://www.djangoproject.com/) is definitely worth
mentioning.

So why not doing it in *Python* instead? Well, because here you can
definitely see that this is a language written by computer scientists
for computer scientists and software developers. You will have a hard
time getting start. It is definitely worth the effort, since the web
most probably won't get smaller in the next years. Compared to the
hours needed to get start with *Shiny*, you will probably need at
least a week to get start with e.g. *Flask*. But than again you have a
more powerful tool at hand to construct your *back end*. (You still
have to develop the *front end*.)

Speaking of which, for the sole **front end** development I recommend
[AngularJS2](https://angular.io/docs/js/latest/quickstart.html). It
uses [TypeScript](http://www.typescriptlang.org/) instead of
*JavaScript* and makes it plain simple to construct interactive web
pages. [jQuery](https://jqueryui.com/) is a tool also worth
mentioning. 

Pretty nice tools. But they come to the price of a considerable
overhead.

Apart from that, you can use *JavaScript* to write hybrid application,
which can be compiled to Android, IOS, web apps as well as to Ubuntu
native applications using [Apache
Cordova](https://cordova.apache.org/docs/en/latest/guide/cli/index.html). If
you are interested, check out my [Mensacard Hack](mensacard-pt-i)
series, in which I use *Cordova* to interact with
[RFID](https://en.wikipedia.org/wiki/Radio-frequency_identification)-based
cards. 

# Outlook

Alright. To wrap things up, I will tell you how in my point of view
the future of some basic components of our workflow and the stuff I
just told you about may look like.

![slide-outlook](/thegreatwhiteshark.coding.io/images/posts/2017/how-much-computer-skills/slide-web-outlook.png)

#### Our beloveth editors

As long as we will use keyboards as a human-computer interface,
**Vim** and **Emacs** will always be around. And as long I can still
type and see, I will do my best to ensure the latter one ;).

#### C++ and Fortran

I doubt they will be around forever. 

Let's focus on **Fortran** first. It is an incredible high-preforming
language and a lot big scientific software is based on it. But in a
lot of cases this software has its origins, like *Fortran* itself, in
the Precambrian age of computing and it just does not make any sense
rewriting them in another language. I do not know any exiting software
project started past 2001 based on *Fortran* and I also do not know
promising young programmers or scientists favoring it either.

Of course Fortran is evolving, has a fully fledged object oriented
framework by now, and since recently you are even able to control the
parallelization using the registers of your CPU via plain Fortran
code! But nevertheless. It feels like it's dying and I would never use
it on my own.

With **C++** it is a different story, since it is so incredible
widespread and you have new ways, like combine it seamlessly with
e.g. *Python* and *R*. Ten years ago I would probably not be able to
imagine a world without C++. But nowadays there are young and aspiring
languages like Mozilla's [Rust](https://www.rust-lang.org/en-US/),
Apple's [Swift](https://swift.org/) and Google's
[Go](https://golang.org/), which do have some mayor benefits over C++
(e.g. being memory-save) and are as fast as C++ itself. Those language
gain more and more support. So let's see where our journey is heading
and which one will become the dominant one. Or maybe C++ will remain
on top of all of them. Who knows...

#### Python and R

In case of those two, I have no idea what will happen. The *Python*
developers tried as hard as they could to *not* make the language last
forever. But they failed. Even the horrible transition between 2.7 and
3 didn't brought it down. Hopefully developers of other languages
learned from their mistakes.

Maybe someday something will come around making prototyping
significantly faster while being as (or even better) performing. But
it hasn't come yet. So at this point of time, I do not see an end of
neither *R* nor *Python*.

#### LaTeX

As I pointed out in the *Markdown*/**pandoc** section, we still have
to tweak the converted drafts to fulfill all your needs and to make
the paper ready for submission. But *pandoc* will improve and future
generations probably won't have to write plain *LaTeX* anymore. It
will definitely be still around, but hidden under a wrapper, or just
used on journal-side.

#### HTML | CSS | JavaScript

These three things are the essentials of the internet. IMHO they will
last as long as the internet itself, or else we would break most of
the existing web pages.

In *HTML* and *CSS* you instead see the trend of improving the
language further and further to adjust to the growing needs of
developers. So learning all three of them will be definitely no waste
of time.

#### Conclusion

So don't be afraid of trying new tools and languages and keep
adapting! The landscape of software frameworks is changing rapidly and
it's easy to be left behind.
