---
name: 下载安装
---

## 下载安装

下载安装前必须应该正确安装 [Go 语言](https://golang.org/) 以及配置 `$GOPATH` 变量。

### 下载源码并编译

使用一个特定版本：

```sh
$ go get gopkg.in/ini.v1
```

使用最新版：

```sh
$ go get github.com/go-ini/ini
```

如需更新请添加 `-u` 选项。

### 测试安装

如果您想要在自己的机器上运行测试，请使用 `-t` 标记：

```sh
$ go get -t gopkg.in/ini.v1
```

如需更新请添加 `-u` 选项。

#### 本地运行测试脚本

进入到源码目录后执行 `make test` 命令（**根据需要替换导入路径**）：

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