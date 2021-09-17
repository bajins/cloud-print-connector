- [Get Ready](#get-ready)
  - [Linux](#linux)
  - [OS X](#os-x)
  - [Windows](#windows)
- [Build](#build)
- [Footnote: Compiling Go](#footnote-compiling-go)

## Get ready

### Linux

We regularly develop and test with Ubuntu 14.04.

[Install Go](https://golang.org/doc/install). You can first try using your distribution's package, but you need version 1.5.2 or newer, so the distribution's package may not be recent enough to build the Cloud Print Connector.  To check what version of Go you have:

```
$ go version
go version go1.5.2 linux/arm
```

Install a few dev packages. The connector uses:
- build tools to interface with C libraries [via cgo](https://golang.org/cmd/cgo/)
- [CUPS](http://www.cups.org/) to, you know, print
- [Avahi](http://avahi.org/) to announce its presence via mDNS/DNS-SD
- [Git](https://git-scm.com/) to clone Git repositories containing Go libraries and the CUPS Connector
- [Bazaar](http://bazaar.canonical.com/) to clone Bazaar repositories containing Go libraries

If your distro is based on Debian (Ubuntu, Raspbian, Mint, others) then this one-liner will get all dependencies:
```
$ sudo apt-get install build-essential libcups2-dev libavahi-client-dev git bzr
```

#### Fedora 22
```sh
$ sudo dnf install gcc cups-devel avahi-devel git bzr
```

Note that building on Fedora might be broken, see https://bugzilla.redhat.com/show_bug.cgi?id=1038683 for more details.

#### OpenSUSE 13.2
```sh
$ sudo zypper install cups-devel avahi-devel git bzr
```

#### CentOS and friends
```sh 
$ sudo yum install gcc cups-devel avahi-devel git bzr
```

### OS X

Install XCode: https://itunes.apple.com/us/app/xcode/id497799835

Install the command line developer tools:

```
$ xcode-select --install
```

Accept the license agreement:

```
$ xcodebuild -license
```

### Windows

[Install MSYS2.](https://msys2.github.io/) Follow the directions to the end to ensure a fully up-to-date MSYS2 installation.

Install some dev packages.

```
$ pacman -S mingw-w64-$(arch)-go mingw-w64-$(arch)-pkg-config mingw-w64-$(arch)-gcc mingw-w64-$(arch)-cairo mingw-w64-$(arch)-poppler git bzr
```

Then open the "MinGW-w64 Win64 Shell" and follow the Build instructions below.

### Other operating systems

It should work on any BSD, but ~10 lines of code need to be adjusted. If you would like to use the connector with a BSD, let us know with GitHub issue.

## Build

```
$ go get github.com/google/cloud-print-connector/...
```

You should now have two new binaries. Windows: `gcp-windows-connector.exe` and `gcp-connector-util.exe`. Everyone else: `gcp-cups-connector` and `gcp-connector-util`.

## Footnote: Compiling Go

If you need to install Go from source (because there isn't a Go binary for your platform at https://golang.org/dl) then this might help. You will need:

- [git](https://git-scm.com/), to clone the GitHub repository
- C toolchain, to compile the Go compiler

If your Linux distro is based on Debian (Ubuntu, Raspbian, Mint, others):

```
$ sudo apt-get install git build-essential
```

### Install the [Go programming language](https://golang.org/) version 1.4
```sh
$ git clone https://go.googlesource.com/go ~/go1.4
$ cd ~/go1.4/src
$ git checkout go1.4.3
$ ./all.bash
```

### Install the Go programming language version 1.5 (this requires Go 1.4; [long story](https://docs.google.com/document/d/1OaatvGhEAq7VseQ9kkavxKNAfepWy2yhPUBs96FGV28/view))
```sh
$ git clone https://go.googlesource.com/go ~/go1.5
$ cd ~/go1.5/src
$ git checkout go1.5.2
$ ./all.bash
```

### Configure Go
```sh
$ echo 'export GOPATH=$HOME/go' >> ~/.bashrc
$ echo 'PATH="$PATH:$HOME/go1.5/bin:$GOPATH/bin"' >> ~/.bashrc
$ source ~/.bashrc
$ go version
go version go1.5.2 linux/arm
```