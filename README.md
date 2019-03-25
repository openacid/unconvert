[![Build Status](https://travis-ci.org/openacid/unconvert.svg?branch=master)](https://travis-ci.org/openacid/unconvert)

# About

> This fork from github.com/mdempsky/unconvert fixed issues with the latest
> "golang.org/x/tools/go/packages" in go-1.10.x
>
> 1. `unconvert` relies on `golang.org/x/tools/go/packages`.
> 1. `go get` in a clean env pulls the latest `golang.org/x/tools/go/packages`.
> 1. The latest `golang.org/x/tools/go/packages`(the master) is for latest go(1.12.x) and it assumes that `go list` is able to accept the argument `-compiled`.
> 1. An older go(e.g. 1.10.x) does not support `-compiled` for `go list`. Thus install unconvert in a clean go-1.10.x env results in an error like above.
>
> By drdr.xp@gmail.com

The unconvert program analyzes Go packages to identify unnecessary
type conversions; i.e., expressions T(x) where x already has type T.

# Install

    $ go get github.com/openacid/unconvert

# Usage

    $ unconvert -v bytes fmt
    GOROOT/src/bytes/reader.go:117:14: unnecessary conversion
                    abs = int64(r.i) + offset
                               ^
    GOROOT/src/fmt/print.go:411:21: unnecessary conversion
            p.fmt.integer(int64(v), 16, unsigned, udigits)
                               ^

# Flags

Using the -v flag, unconvert will also print the source line and a
caret to indicate the unnecessary conversion's position therein.

Using the -apply flag, unconvert will rewrite the Go source files
without the unnecessary type conversions.

Using the -all flag, unconvert will analyze the Go packages under all
possible GOOS/GOARCH combinations, and only identify conversions that
are unnecessary in all cases.

E.g., syscall.Timespec's Sec and Nsec fields are int64 under
linux/amd64 but int32 under linux/386.  An int64(ts.Sec) conversion
that appears in a linux/amd64-only file will be identified as
unnecessary, but it will be preserved if it occurs in a file that's
compiled for both linux/amd64 and linux/386.
