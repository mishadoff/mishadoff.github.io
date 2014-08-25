---
layout: post
title: "Clojure + Emacs + Leiningen + nREPL"
date: 2012-10-13 18:19
comments: true
categories: [clojure, emacs, lein, nrepl, ide]
---

This article provides step-by-step instruction how to configure Clojure environment.

<!-- more -->

### Little history

Long time I was using [swank-clojure](https://github.com/technomancy/swank-clojure) as connector to my Clojure projects from Emacs [SLIME](http://common-lisp.net/project/slime/).
But, suddenly, I lost all my emacs preferences and needed to configure it again (*yeah, it's my fault, this time I've created configuration on GitHub*).
So, when I entered to swank-clojure project page, I was a bit frustrated by the following message:
*"This project is no longer under active development. New users are strongly encouraged to try out* [nrepl.el](https://github.com/kingtim/nrepl.el) *instead"*.
Ok, let's see what nrepl is.

### Why emacs?

I don't know. I like lisp, and emacs created in (e)lisp.
Strange hotkeys like `C-x C-s`. Huge functionality, that you can configure all days. Internal package manager. Plenty of modes.
Mail client. Games. Whatever. Oh wait, I forgot text editor. I really, can't find any great reason that convince somebody to use emacs as IDE. But I like it.

There is a good [xkcd](http://xkcd.com/378/) comics about emacs.

By the way, there are lot of IDEs that can be configured much simpler way than described in this article:

* [Clojure Box](http://clojure.bighugh.com/) - emacs install, preconfigured for clojure. Only for Windows
* [Counter Clockwise](http://code.google.com/p/counterclockwise/) - eclipse plugin
* [Enclojure](http://www.enclojure.org/) - netbeans plugin
* [La Clojure](http://plugins.intellij.net/plugin/?id=4050) - IDEA plugin
* [VimClojure](http://www.vim.org/scripts/script.php?script_id=2501) - vim plugin
* [Light Table](http://www.chris-granger.com/2012/04/12/light-table---a-new-ide-concept/) - new generation of IDEs?

So, before reading, ask yourself, Do I really need emacs?

### Emacs

First of all you need to install emacs, to get your future clojure coding environment. I'm using ubuntu, so I just type:

`sudo apt-get install emacs`

and it install emacs 23. If you using another OS, or do not trust package manager,
check [GNU Emacs official website](http://www.gnu.org/software/emacs/) for installation instructions.

Also, you need to install emacs package-manager.

1. Copy file [package.el](http://repo.or.cz/w/emacs.git/blob_plain/1a0a666f941c99882093d7bd08ced15033bc3f0c:/lisp/emacs-lisp/package.el)
to your `~/emacs.d/` folder.
2. In your `~/.emacs` file add folowing lines:

```
(add-to-list 'load-path "~/.emacs.d/")
(load "package")
(require 'package)
(add-to-list 'package-archives
    '("marmalade" .
      "http://marmalade-repo.org/packages/"))
(package-initialize)
```

These lines will be loaded after emacs restarted and install package manager.
To check if it works, type `M-x package-list-packages` and this show you list of emacs plugins, hope you will find something useful there.

### Leiningen

[Leiningen](http://leiningen.org/) is a build tool for clojure. It also controls dependency management, automation, tests, etc.
Pretty like [maven](http://maven.apache.org/) for Java.

To install lein 2 (it's shorter name for Leiningen) do the following:

1. Download [lein script](https://raw.github.com/technomancy/leiningen/preview/bin/lein)
2. Place it on your `$PATH`
3. Make it executable

On ubuntu this can be performed with the following commands:

    cd ~/
    mkdir bin

Add line export `PATH=~/bin:$PATH` to your `~/.bashrc` file

    cd ~/bin
    wget https://raw.github.com/technomancy/leiningen/preview/bin/lein
    chmod +x lein

Execute lein, for first time it will download default lein package. To verify installation run `lein version`.
On my machine for the post date it prints: `Leiningen 2.0.0-preview10 on Java 1.6.0_24 OpenJDK 64-Bit Server VM`

### nREPL

We have already installed emacs package manager, so we can get nrepl from there.
It also needs emacs major mode `clojure-mode`, so type the following commands in emacs to install needed packages:

    M-x package-install clojure-mode
    M-x package-install nrepl

Try `M-x nrepl-jack-in` to execute REPL, where you can play with clojure commands.

### Bind all together

We have fully configured environment for clojure and how to use it?

Create new clojure project using lein:

`lein new helloworld`

Start nREPL server in emacs:

`M-x nrepl-jack-in`

Open core project file in emacs:

`C-f helloworld/src/helloworld/core.clj`

Write simple function:

``` clojure
(ns helloworld.core)
(defn hello []
  (println "Hell, O'World!"))
```

Compile it:

`C-c C-k`

Switch to nREPL buffer, change namespace and run hello function:

``` clojure
(ns helloworld.core)
(hello)
```

Probably, you'll get output:

    Hell, O'World!
    nil

**Congratulations!** You just created working clojure project.

**Note:** this configuration covers only basic clojure+emacs environment.
There are plenty plugins to work with parenthesis, documentation, tests, auto-completion and lot more.
I'll do my best to cover such plugins in the next sections. Also, emacs23 is an old version of emacs, verison 24
is currently available.
