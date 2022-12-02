---
title: "A Beginners Thoughts on Releasing Software"
date: 2022-12-02T00:20:16-06:00
draft: false
---

I wanted to share my experience with uploading my first package, excite-cli, to the Arch User Repository (AUR). While the process was fairly straightforward (especially with [this](https://wiki.archlinux.org/title/Creating_packages) handy guide on the Arch Wiki on creating packages), I ran into several issues along the way. Most of my confusion arose from project-specific design choices or my own misunderstanding, but there were some legitimately confusing parts that I think are worth explaining. Hopefully this can serve as a quickstart and provide inspiration for releasing your own project on a package manager.

**Before publishing a package, please be sure to understand the proper [submission guidelines](https://wiki.archlinux.org/title/AUR_submission_guidelines) and [package etiquette](https://wiki.archlinux.org/title/Arch_package_guidelines).**

---

## Setup and Authentication

The first thing we'll need to do is to create an AUR account. All that we really need is an email address and a public SSH key, the latter of which is necessary for submitting packages. Once that's all taken care of, we need to create a local git repository of the AUR remote by cloning the pkgbase. Assuming that we are creating a new package, the cloned repository *should be empty*.

```sh
$ git clone ssh://aur@aur.archlinux.org/excite-cli.git

Cloning into 'excite-cli'...
warning: You appear to have cloned an empty repository.
Checking connectivity... done.
```

*Note that `excite-cli` is the pkgbase. Replace with the desired package name.*

Next, we can establish a remote connection with `git remote add excite ssh://aur@aur.archlinux.org/excite-cli.git`. Then a simple `git fetch excite` will initialize it in the AUR.

Okay, not so bad, right? So far we've just been following the AUR submission guidelines on the Arch Wiki. But, there are a couple of things to keep in mind. 

First, we cannot have **any** nested directories in our AUR repo. My initial thought was to just add a remote to my existing project repository, but unfortunately it has too many subdirectories. In fact, there should really only be two files that live in the package repository - PKGBUILD and .SRCINFO (more on these later). Presumably this is to save space and so that packages are submitted with only what is strictly necessary, which in most cases is just the build script and package info. I initialized the package repository in a subdirectory of my main repo called `build` so that I could have everything in one place.

The second bit that threw me off - the branch you push to the AUR from **must be named "master."** For some reason I didn't realize this until I came across a couple rather political forum posts about why they refuse to change the default branch to main ([1](https://bbs.archlinux.org/viewtopic.php?id=257186),[2](https://bugs.archlinux.org/task/72257)). The official reason, given by one of the maintainers, is as follows: 

> Reason for closing:  Won't implement
Additional comments about closing:  We've been using master for 15 years, and we aren't going to support main just because other projects have decided to suggest this as a default. 

Ummm, alright then, seems a little dogmatic. Typing two fewer letters to switch branches seems like an improvement to me, but in their defense, it seems like this might not be such a simple change after all. A few of the comments point out that it's baked into the AUR web back-end.

---

## Build Tools

Anyway, let's transition into the build tools. The AUR uses a build script called `makepkg` to automate the building of packages. If you're on an Arch-based system, you should have this provided through pacman by default. As I hinted at previously. the only two files essential for building an AUR package are the PKGBUILD script and the .SRCINFO. When makepkg is run, it searches the current directory for the PKGBUILD script to build and install the package. To get started, I'd recommend checking out the example PKBUILDs located in `/usr/share/pacman/` and using [this](https://wiki.archlinux.org/title/PKGBUILD) guide for more detail. Here's my PKGBUILD, which is a much more paired-down version of the examples:

```sh
# Maintainer: nshan651 <nshan651@aur.archlinux.org>
pkgname=excite-cli
# Note that version cannot have any hyphens '-'
pkgver=v0.2.0_alpha 
# +1 for every release that doesn't involve a version change. 
# Reset to '1' when changing pkgver.
pkgrel=1 
pkgdesc="A Terminal-Based Citation Generator"
# System architecture
arch=('x86_64') 
# Source url. Prefix with 'git+' when pointing to a git repo
url="git+https://github.com/nshan651/excite-cli" 
license=('GPL3')
# Dependencies to run package and make dependencies
depends=('lua>=5.1') 
makedepends=() 
# Source to pull. Generally your $url or a tar.gz
source=("$url") 
md5sums=('SKIP')

build() {
    # Alternative: "$pkgname-$pkgver"
    cd "$pkgname" 
    make
}

package() {
    cd "$pkgname" 
    make DESTDIR="$pkgdir/" install
}
```

The first thing that's really important to have is the maintainer(s) listed at the top as a comment with their username and email (Make sure you mask your email to protect against spam). After carefully adding the settings for our package (as well as any additional options you need), we can write functions to build and package the software. 

We're retrieving, extracting, and then creating a working copy of `$source`, so we need to first cd into `$pkgname`. From here we simply run `make` and any additional scripts. In the package() function, we again need to enter our working git repo, but this time we will need to enter a [fakeroot](https://www.unix.com/man-page/linux/1/fakeroot/) environment to install the packages. Your build scripts will be fairly subjective to your project structure, but I'll show my Makefile as an example:

```sh
TARGET = excite-cli
SRC := src/

all: 
    # Ignore this part for now! 
    # Script that compiles src into a single lua bytecode executable.
	@-./compile.sh 
    # Prepend executable with shebang
	sed -i '1i #!/bin/lua' $(SRC)excite 

install:
	mkdir -p $(DESTDIR)/usr/local/bin
	install -Dm755 $(SRC)excite $(DESTDIR)/usr/local/bin/excite

clean:
	rm -rf $(SRC)excite build/pkg build/src build/excite* 
```

The only detail that's pertinent to our discussion right now in the first part of my Makefile is that the executable is being created in the `src/` directory (see the **afterward** for more if you're curious). When we call `make install` in the package() function of PKGBUILD, we pass in `$pkgdir`, which will set the destination to the name of the package by default. The [install](https://www.mankier.com/1/install) program can be used to copy the files and set their attributes:

* `-D` creates all leading components of DEST except the last, or all components of --tar‐get-directory, then copy SOURCE to DEST
* `-m` sets the permission mode, which we supply with 755 to give our package executable permissions

If we run the `makepkg` utility, we can see several new additions in the build directory:

```
~/git/excite-cli/
│   citation.txt
│   compile.sh 
│   LICENSE 
│   logo.png 
│   Makefile
│   README.md
│
└───build/
│   │   PKGBUILD  
│   │   # Running 'makepkg' builds a tar file here
│   │   excite-cli-v0.2.0_alpha-1-x86_64.pkg.tar.zst
│   │  
│   └───excite-cli/
│   │     # Holds a bunch of git info for the AUR
│   │      ...
│   └───src/
│   │      # Working git repo where we build and install
│   │      ...
│   └───pkg/
│       │     
│       │   
│       └───excite-cli/usr/local/bin
│           │ # The executable is installed here 
│           │ excite
│
└───src
    │ # Top-level src files
    │  ... 
    │   
# ... Other top-level directories
```

Upon successfully running `makepkg`, three directories are created containing some git info for the AUR, our working git repository, and the executable file. Run the `make rm` command we created earlier to clean up afterward.

---

## Submitting the Package

Almost done! All that's left to do is to add the .SRCINFO file and push our changes. Since I didn't explain the .SRCINFO yet, it just provides the AUR webpage for our package with useful information to display. Run the following from inside the local repo:

```sh
$ makepkg --printsrcinfo > .SRCINFO
$ git add PKGBUILD .SRCINFO # The only two files that should be pushed!
$ git commit -m "Useful commit message"
$ git push
```

Congrats, you just uploaded your first AUR package! **Please make sure to double-check that you're following the proper [submission guidelines](https://wiki.archlinux.org/title/AUR_submission_guidelines) and [package etiquette](https://wiki.archlinux.org/title/Arch_package_guidelines).**

---

#### Extra: Compiling Lua to Bytecode

* Coming soon!!!


