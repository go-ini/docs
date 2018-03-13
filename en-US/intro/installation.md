---
name: Installation
---

## Installation

You must have [Go](https://golang.org/) installed properly on your machine with correct `$GOPATH` setup.

### Download the package

To use a tagged revision:

```sh
$ go get gopkg.in/ini.v1
```

To use with latest changes:

```sh
$ go get github.com/go-ini/ini
```

Please add `-u` flag to update in the future.

### Testing

If you want to test on your machine, please apply `-t` flag:

```sh
$ go get -t gopkg.in/ini.v1
```

Please add `-u` flag to update in the future.

#### Running tests on your local machine

Go to the directory of the package and execute `make test` command (**replace import path as needed**):

```sh
$ cd $GOPATH/src/gopkg.in/ini.v1
$ make test
go test -v -cover -race
=== RUN   Test_Version

  Get version ✔


1 total assertion

--- PASS: Test_Version (0.00s)
=== RUN   Test_isSlice

  Check if a string is in the slice ✔✔


3 total assertions

--- PASS: Test_isSlice (0.00s)
=== RUN   TestEmpty

...

--- PASS: Test_Duration (0.00s)
PASS
coverage: 94.3% of statements
ok  	gopkg.in/ini.v1	1.121s
```