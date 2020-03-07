---
name: Introduction
---

# INI [![Build Status](https://travis-ci.org/go-ini/ini.svg?branch=master)](https://travis-ci.org/go-ini/ini)  [![](https://sourcegraph.com/github.com/go-ini/ini/-/badge.svg)](https://sourcegraph.com/github.com/go-ini/ini?badge)

![](https://avatars0.githubusercontent.com/u/10216035?v=3&s=200)

Package `ini` provides INI file read and write functionality in Go.

### Features

- Load from multiple data sources (file, `[]byte`, `io.Reader` and `io.ReadCloser`) with overwrites.
- Read with recursion values.
- Read with parent-child sections.
- Read with auto-increment key names.
- Read with multiple-line values.
- Read with tons of helper methods.
- Read and convert values to Go types.
- Read and **WRITE** comments of sections and keys.
- Manipulate sections, keys and comments with ease.
- Keep sections and keys in order as you parse and save.

### Installation

The minimum requirement of Go is **1.6**.

```sh
$ go get gopkg.in/ini.v1
```

Please add `-u` flag to update in the future.

### Getting Help

- [API Documentation](https://gowalker.org/gopkg.in/ini.v1)
- [File An Issue](https://github.com/go-ini/ini/issues/new)
