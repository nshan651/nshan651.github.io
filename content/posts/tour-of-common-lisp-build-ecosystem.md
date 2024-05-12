---
title: "The Hitchhiker's Guide to Building Common Lisp Applications"
date: 2024-04-12T11:22:34-05:00
draft: false 
toc: true
---

Recently, I've been trying to learn Elisp and Common Lisp. I've been curious about the Lisp family of programming languages for quite a while, particularly because of their unique set of features such as treating code as data ([homoiconicity](https://en.wikipedia.org/wiki/Homoiconicity)) and the powerful [macro system](https://en.wikipedia.org/wiki/Lisp_(programming_language)#List_structure_of_program_code;_exploitation_by_macros_and_compilers). I've also been adding to my Emacs configuration quite a bit lately, so it would be great to get a bit more comfortable writing Elisp. 

I'd like to present a practical guide on setting up your first Common Lisp package. I won't be focusing on language syntax or semantics, as I think there are already plenty of instructive videos and blog posts (links to a couple below). I'll assume you at least have a basic understanding of Lisp code. As a new user, I had a hard time finding practical guides on actually setting up a project, so hopefully this can serve as an end-to-end installation and setup guide.

{{% toc %}}

## Installation and Configuration

### SBCL

The first thing you'll want to do is to choose a Common Lisp distribution. There are several [implementations](https://en.wikipedia.org/wiki/Category:Common_Lisp_implementations), but I opted to go with [Steel Bank Common Lisp](http://www.sbcl.org/), as it's generally considered to be the most popular. 

You can choose to build it from source, or install it with your favorite package manager. I'm on Arch (btw), so I'll use `pacman`.

```sh
# Arch
sudo pacman -S sbcl

# Debian
sudo apt install sbcl
```

One mildly annoying thing about SBCL (and most of the following programs) is that it doesn't respect the XDG base spec. I'm sure most people couldn't care less about polluting their `$HOME` directory with dotfiles, but I like a clean home, so I'll also show you how to change the configuration locations. For SBCL, just add the following to the system-wide config, `/etc/sbclrc`:

```lisp
(require :asdf)
(setf sb-ext:*userinit-pathname-function*
      (lambda () (uiop:xdg-config-home #P"sbcl/sbclrc")))
```

### Packaging with Quicklisp

[Quicklisp](https://www.quicklisp.org/beta/) is SBCL's main library manager. Although quicklisp is technically "unofficial", it works with most implementations of Common Lisp, and is essentially the *de facto* library manager. It's also apparently still in beta after nearly 15 years of development, but I'd still say it's further along than other proposed solutions such as [Roswell](https://roswell.github.io/). I do plan on digging deeper into Roswell in the future, but for now let's stick with quicklisp for packaging.

Once again, I would recommend installing quicklisp with a package manager. And just like SBCL, quicklisp slaps the `~/quicklisp` directory right in your your `$HOME` directory (and it doesn't even have the courtesy of making it a hidden dir).

Fortunately, the files in `~/quicklisp` are self-contained, so moving it somewhere more sensible is easy. First, move the `~/quicklisp` directory to the desired location. Next, load the `setup.lisp` file contained by running `sbcl --load setup.lisp` from within the `~/quicklisp` directory:

```lisp
;; From within the REPL, run the following:
* (ql:add-to-init-file)
I will append the following lines to #P"/home/nshan651/.config/sbclrc":

  ;;; The following lines added by ql:add-to-init-file:
  #-quicklisp
  (let ((quicklisp-init (merge-pathnames ".local/opt/quicklisp/setup.lisp"
                                         (user-homedir-pathname))))
    (when (probe-file quicklisp-init)
      (load quicklisp-init)))

Press Enter to continue.
```

There's one more thing we have to do before we can move on. We need to tell quicklisp where our lisp projects are located. By default, quicklisp will search in `./quicklisp/local-projects` directory. I like to throw all my coding projects into `~/git`, so we need to add one more line to our `.sbclrc`:

```lisp
;;; Tell quicklisp to search ~/git/ dir for CL projects.
;;; Note that the trailing "/" is required.
(push "~/git/" ql:*local-project-directories*)
```

### The ASDF Build System

Now that we have external dependencies covered, we need [ASDF](https://asdf.common-lisp.dev/asdf.html) for managing system dependencies at the project level. ASDF provides a framework for loading Lisp *systems*, which are just collections of related Lisp files.

Unlike quicklisp, ASDF is included with SBCL and most other Common Lisp implementations. By default, ASDF will look for systems in the `~/common-lisp` directory. You know the drill by now, let's change that! We need to create a new file located at `~/.config/common-lisp/source-registry.conf.d/50-user.conf`.

Then, just copy in the following, replacing the path with the full path to your projects directory:
```lisp
(:tree "/home/nshan651/git/")
```

That's all! Just a word of caution, if you're following the [documentation](https://asdf.common-lisp.dev/asdf.html#Configurations), they suggest that the source registry can also be configured from the user file `~/.config/common-lisp/source-registry.conf`. For some reason it was only able to read my config when I placed it in the *.d sub-directory, but maybe I'm missing something.

## Setting Up a Project

The average project's structure should look something like this:

```
my-project/
  src/
    my-project.lisp
  t/
    my-project.lisp
  .gitignore
  README.md
  my-project.asd
```
- Note that the common convention is to name the tests folder "t". 

The main point of entry into our program where we will define our system is the *.asd file. Sometimes this will be broken up into two (one file for src and the other for tests), but putting everything in one file is fine for small projects. Let's go ahead and define a system for our project:

```lisp
(defsystem "rcv"
  :author "nshan651 <public.nshan651.com>"
  :license "AGPL 3.0"
  :version "0.1"
  :homepage "https://github.com/nshan651/rcv"
  :bug-tracker "https://github.com/nshan651/rcv/issues"
  :source-control (:git "git@github.com/nshan651/rcv.git")
  :description "Ranked-choice voting command-line tool."
  :depends-on ("clingon"
	           "cl-csv"
	           "uiop")
  :components ((:module "src"
	            :serial t
		        :components ((:file "main"))))
  :long-description
  #.(uiop:read-file-string
     (uiop:subpathname *load-pathname* "README.md"))
  :in-order-to ((test-op (test-op "rcv/tests"))))
```

Most of the keyword definitions are self-explanatory, but there are a couple things to note. The dependencies this project happens to be using are [clingon](https://github.com/dnaeon/clingon) (command-line options parser), [cl-csv](https://github.com/AccelerationNet/cl-csv) (csv parser), and [uiop](https://github.com/fare/asdf/blob/master/uiop/README.md) (portability layer on top of ASDF with extra utilities). You will likely have different and/or additional dependencies, so add them to `:depends-on`. 

- `:serial t`
  - For your own component modules (any sub-directories in your project containing relevant lisp code), you can specify the `:serial t` key, which tells ASDF to treat each subsequent file as depending on the previous ones. To put it simply, it loads your lisp files sequentially. My project only has one file, so this isn't necessary just yet. However, as your project continues to grow, `:serial` can be a useful shorthand for dependency management.
- `:long-description`
  - ASDF will embed the contents of the `README.md` into the [system definition at read-time](https://lisp-lang.org/learn/writing-libraries#defining-systems).

### Compiling Binaries

One thing I like to consider when writing tools is portability, and compiling programs into binary executables can be a great way to distribute code. The nuances of creating self-contained executables can be quite complex, so I'll try to keep it brief and highlight a couple important things to look out for. For a more comprehensive breakdown, read [this page from the Lisp Cookbook](https://lispcookbook.github.io/cl-cookbook/scripting.html).

In the `defsystem` we've just created in `rcv.asd`, add the following three directives.
```lisp
:build-operation "program-op"
:build-pathname "bin/rcv"
:entry-point "rcv::main"
```

Now we can build an executable from SBCL by loading our system with `(ql:quickload :rcv)` and calling `(asdf:make :rcv)`. Keep in mind that saving images or executables **does not work when done from an mREPL such as SLIME or Sly**. Executables typically need to be [compiled from sbcl directly](https://stackoverflow.com/questions/47888877/sbcl-building-a-standalone-executable).

It's still a little cumbersome to spin up an sbcl repl every time we want to test our executable, so let's create a Makefile.
```Makefile
# Binary executable.
BIN := rcv
# Lisp implementation.
LISP := sbcl

.PHONY: all run

all: 
	$(LISP)	--non-interactive \
		--eval '(ql:quickload :rcv)' \
		--eval '(asdf:make :rcv)'

run:
    # Note: '@' suppresses the echoing of the command itself.
	@ ./bin/$(BIN)
```

Let's create additional rules for [installing](https://man7.org/linux/man-pages/man1/install.1.html) and uninstalling the freshly-minted executable binary to `/usr/local/bin`.
```Makefile
install:
	# Note: Create /usr/bin/local if it doesn't exist;
	#       Sets read, write, and execute priviledges for the owner
	install -Dm755 ./bin/$(BIN) $(DESTDIR)/usr/local/bin/$(BIN)

uninstall:
	rm -f $(DESTDIR)/usr/local/bin/$(BIN)

clean: uninstall
	rm -f ./bin/$(BIN)
```

Lastly, I highly building with SBCL's [core compression](https://lispcookbook.github.io/cl-cookbook/scripting.html#building-a-smaller-binary-with-sbcls-core-compression) to **greatly** reduce the size of your binaries. It costs virtually nothing, and is quite straightforward to include.

First, check if your SBCL was built with core compression.
```lisp
(find :sb-core-compression *features*)
:SB-CORE-COMPRESSION
```

Now simply add the following to the end of your .asd (make sure it's outside of an `defsystem` calls):
```lisp
#+sb-core-compression
(defmethod asdf:perform ((o asdf:image-op) (c asdf:system))
  (uiop:dump-image (asdf:output-file o c)
                   :executable t
                   :compression t))
```

As far as I can tell, there isn't any reason to *not* build with core compression, unless the handful of milliseconds it adds to the startup time really is a deal-breaker for your application. In this case, it reduced our executable size from 69 MB to just 14 MB, at a cost of 80 ms. Keep in mind that this is just the *startup time*, so I imagine that as your program grows in complexity, this trade-off begins to look even more appealing.

### Additional Notes on Compilation

There's an important distinction between executables and images in Common Lisp. Lisp images are snapshots of a running Lisp environment, including not only compiled code, but also the current state of the Lisp system, including loaded libraries, defined functions and variables, and the current execution context. While images are incredibly useful for batch computing tasks and development, they're not typically used for deployment as they are naturally quite a bit larger and slower than a traditional binary.

An issue you may come across is in dealing with dynamic library dependencies. Standalone executables built with shared library dependencies may not necessarily work on all machines out of the box. For these situations, I recommend using [deploy](https://github.com/Shinmera/deploy), which auto-detects foreign dependencies and places them in the `bin/` directory of your project. Fortunately our project doesn't have any foreign dependencies, but adding `deploy` is a simple matter of changing a few lines in the asd file:
```lisp
;;;; From the Lisp Cookbook:
:defsystem-depends-on (:deploy)              ; (ql:quickload "deploy") before
:build-operation "deploy-op"                 ; instead of "program-op"
:build-pathname "my-application-name"        ; doesn't change
:entry-point "my-package:my-start-function"  ; doesn't change
```

## NOTES

- Cannot save an image with multiple threads running:
  - http://www.sbcl.org/manual/#Saving-a-Core-Image
  - https://stackoverflow.com/questions/47888877/sbcl-building-a-standalone-executable
- FASL files:
  - https://www.common-lisp.com/support/documentation/current/compiling.html#22-fasl-files
- CL cookbook on scripting and binaries:
  - https://lispcookbook.github.io/cl-cookbook/scripting.html

### Project Testing

```lisp
(defsystem "rcv/tests"
  :description "Test suite for rcv."
  :author "Nick Shannon <public.nshan651.com>"

  :license "AGPL 3.0"
  :version "0.1"

  :depends-on ("rcv"
	       "fiveam")
  :components ((:module "t"
                :serial t
                :components ((:file "main")))))
```

#### 5am

## Closing Thoughts

## Helpful Resources

- [The Common Lisp Style Guide](https://lisp-lang.org/style-guide/)
- [A Road to Common Lisp](https://stevelosh.com/blog/2018/08/a-road-to-common-lisp/)
- [Small Common Lisp CLI Programs](https://stevelosh.com/blog/2021/03/small-common-lisp-cli-programs/) 
- [Writing Libraries](https://lisp-lang.org/learn/writing-libraries)
- https://www.common-lisp.com/support/documentation/current/compiling.html#22-fasl-files
- https://www.lispworks.com/documentation/lw60/LW/html/lw-95.htm

## Bootstrap Script to Set Up the Above Recommendations

```sh
#!/bin/sh


```
