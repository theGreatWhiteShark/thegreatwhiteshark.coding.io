+++
date = "2017-07-12T17:55:38+01:00"
draft = false
comment = true
type = "post"
tags = [ "python", "deep learning" ]
slug = "wxArt"
title = "wxArt"
subtitle = "A wxpython interface to the Neural art style transfer"
description = "In this project some friends of mine created a python-based GUI to take photographs via a camera of a laptop or an external one, to perform a Neural style transfer using a deep convolutional neuronal network, and to print a postcard of the result."
+++

![wxArt](/thegreatwhiteshark.coding.io/images/posts/2017/wxArt/screenshot.jpg)

The paper [A Neural Algorithm of Artistic
Style](https://arxiv.org/abs/1508.06576) by Leon A. Gatys, Alexander
S. Ecker, and Matthias Bethge in 2015 was a game changer. It was not
just one of the major sparks to light up the interest in deep
learning in general, it also was one of the reasons Stephan and I
started a reading group on machine learning back in the beginning of
2016. And we are still committed to it and meet at an almost weekly
basis at time of writing (24.2.2018).

Some friends of mine created a wrapper around the code of
[yusuketomoto](https://github.com/yusuketomoto/chainer-fast-neuralstyle)
using the models given by
[gafr](https://github.com/gafr/chainer-fast-neuralstyle-models) to
have their own wxpython-based GUI to perform the style transfer. This
software was presented at the German day of unification in 2016 in
Dresden. Since then it became a tradition to use it during the
annually exhibition of projects at our institute in Dresden. 

In 2017 it was my turn to organize our groups part of the exhibition
[Dresdner Lange Nacht der
Wissenschaften](http://www.wissenschaftsnacht-dresden.de/) (the long
night of science in Dresden) and it came along with repairing and
polishing some parts of the code. But all the credit belongs to Justus
and Bene. Not me.

Using the software you can make a photograph with either the internal
camera of your laptop or an external one, perform a style transfer,
and printing the result as a postcard. Due to this giveaway this
project is always one of the main attraction using the
exhibition. There are 11 different styles to pick, which were trained
on great pieces of famous painters or iconic works, like
Kandinsky, Kirchner, Dürrer, or the Great Wave off Kanagawa (and that's
the story of my [Github profile picture](https://github.com/theGreatWhiteShark)).
 
The setup for the image above, on the other hand, was done by me and
just for this single picture my whole contribution did pay off :smile: .
