This repositories covers most of the parts of my
[blog](https://thegreatwhiteshark.github.io/) on coding, hacking, and
data science. 

It is based on the awesome static website generator
[Hugo](https://gohugo.io/) and the
[casper](https://github.com/vjeantet/hugo-theme-casper) theme.

You can access the blog via
[https://thegreatwhiteshark.github.io/thegreatwhiteshark.coding.io/](https://thegreatwhiteshark.github.io/thegreatwhiteshark.coding.io/).

# Installation

In order to use this repository as a basis for your own blog, first,
clone it including all of its submodules.

``` bash
git clone https://github.com/theGreatWhiteShark/thegreatwhiteshark.coding.io.git
cd thegreatwhiteshark.coding.io
git submodule update --init --remote --recursive
```

You can now serve the blog by running Hugo as a server from the
command-line.

``` bash
hugo server
```

In order to write your own customization the
**/static/css/scss/custom.scss** file of the package, you first have
`node-sass`. It will translate the .scss file into a .css one, which
is imported by Hugo. Since the latter is also building the website
anew every time one of the served files changes, this makes modifying
your blog incredible fast.

``` bash
npm install
npm run build
```

# Customization

Just cloning the app and uploading its content to a different
repository won't result in a blog provided by [Github
Pages](https://pages.github.com/). Firstly, you have to add your own
[encrypted](http://rcoedo.com/post/hugo-static-site-generator/) SSH
key in order to allow [Travis](https://travis-ci.org/) to write the
*gh-pages* branch of your repository. 
