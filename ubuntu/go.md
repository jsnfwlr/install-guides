

# How To Install Go on Any Version of Ubuntu

### Introduction

[Go](https://golang.org) is a modern programming language developed at Google. It is increasingly popular for many applications ([docker](https://github.com/docker/docker-ce), [kubernetes](https://github.com/kubernetes/kubernetes), and more) and at many companies, and offers a robust set of libraries. This tutorial will walk you through downloading and installing the latest version of Go (Go 1.15 at the time of this article’s publication).

## Prerequisites

This tutorial assumes that you have access to an Ubuntu system later than 16.04, configured with a non-root user with sudo privileges as described in [Initial Server Setup with Ubuntu 20.04.](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04)


## Step 1 — Downloading Go

In this step we will connect to your Ubuntu system, download an archive of the latest version of Go from the [Go website](https://golang.org), extract it, and move the extracted `go` folder from your `/tmp` directory to your `/usr/local` directory as a versioned folder.

```bash
ssh <^>user<^>@<^>your_system_address<^>
```

In order to install Go, you’ll need to grab the latest version from [the official Go downloads page](https://golang.org/dl/). On the site you can find the URL for the current binary release’s archive, along with its *SHA256 Checksum*.

```bash
cd /tmp
wget https://golang.org/dl/<^>go1.15.8.linux-amd64.tar.gz<^>
```

Next, you can use `sha256sum` to verify the archive's authenticity and integrity:

```bash 
sha256sum <^>go1.15.8.linux-amd64.tar.gz<^>
```

```
[secondary_label sha256sum Output]
<^>d3379c32a90fdf9382166f8f48034c459a8cc433730bc9476d39d9082c94583b<^>  go1.15.8.linux-amd64.tar.gz
```

This will output a string like the one highlighted in the above output, which can be compared the the *SHA256 Checksum* from the download website.

Next, extract the contents of the archive using `tar`

```bash
tar xvf <^>go1.15.8.linux-amd64.tar.gz<^>
```

Here, the `x` flag tells `tar` to extract the file, `v` tells it to be verbose by listing the files being extracted, and `f` tells it which file to extract from.

You should now have a directory called `go` in your `/tmp` directory.

Now we will recursively change the owner and group of the `go` directory and its content to **root** and move it to `/usr/local`, renaming it based on the version as we do.

```bash
sudo chown -R root:root ./go
sudo mv ./go /usr/local/<^>go1.15.8<^>
```

Next, 


## Step 2 — Installing Go

In this step we will create a symbolic link from the versioned folder in `/usr/local`, and create the appropriate symbolic links to the `go` and `gofmt` binaries.

First, we will create a symbolic link in `/usr/local` that can be used to change versions of `go` in the future.

```bash
ln -s /usr/local/<^>go1.15.8<^> /usr/local/go
```

Here the _link_ command `ln` is given the `-s` flag, telling it to make a symbolic link from `/usr/local/go` pointing to `/usr/local/<^>go1.15.8<^>`. Symbolic links are a great way of creating aliases that point from one part of a file system to another part of a file system.

Next, we will create symbolic links in `/usr/local/bin` that point to the `go` and `gofmt` binaries under the symbolic link `go` folder we created in [Step 1](#step-1-—-installing-go)

```bash
ln -s /usr/local/go/bin/go /usr/local/bin/go
ln -s /usr/local/go/bin/gofmt /usr/local/bin/gofmt
```

With all these symbolic links created, when you need to update the version of Go you are using in the future, you can simply follow [Step 1](#step-1-—-installing-go) and then remove the symbolic link to the versioned `go` folder, and recreate it to point at the new versioned `go` folder.

```bash
rm /usr/local/go
ln -s /usr/local/<^>go1.16.0<^> /usr/local/go
```

## Step 3 — Configuring your environment

In this step we will enable [Go Modules](https://blog.golang.org/using-go-modules).

To enable Go Modules, all you need to do is set the environment variable `GO111MODULE` to `on`

```bash
printf "\nGO111MODULE=on\n" ~/.profile
```

To verify this has been successful, run the `go env` command and look for the `GO111MODULE` environment variable in the output:

```bash
go env
```

```
[secondary_label go env Output]
GO111MODULE="on"  
GOARCH="amd64"  
GOBIN=""  
GOCACHE="/home/superadmin/.cache/go-build"  
GOENV="/home/superadmin/.config/go/env"  
GOEXE=""  
GOFLAGS=""  
GOHOSTARCH="amd64"  
GOHOSTOS="linux"  
GOINSECURE=""  
GOMODCACHE="/home/superadmin/go/pkg/mod"  
GONOPROXY=""  
GONOSUMDB=""  
GOOS="linux"  
GOPATH="/home/superadmin/go"  
GOPRIVATE=""  
GOPROXY="https://proxy.golang.org,direct"  
GOROOT="/usr/local/go"  
GOSUMDB="sum.golang.org"  
GOTMPDIR=""  
GOTOOLDIR="/usr/local/go/pkg/tool/linux_amd64"  
GCCGO="gccgo"  
AR="ar"  
CC="gcc"  
CXX="g++"  
CGO_ENABLED="1"  
GOMOD="/dev/null"  
CGO_CFLAGS="-g -O2"  
CGO_CPPFLAGS=""  
CGO_CXXFLAGS="-g -O2"  
CGO_FFLAGS="-g -O2"  
CGO_LDFLAGS="-g -O2"  
PKG_CONFIG="pkg-config"  
GOGCCFLAGS="-fPIC -m64 -pthread -fmessage-length=0 -fdebug-prefix-map=/tmp/go-build751789766=/tmp/go-build -gno-record-gcc-switches"
```
