---
name: Load from data sources
---

## Load from data sources

As advertised, load from different data sources is possible and effortless.

So, what is a **Data Source** anyway? 

A **Data Source** is either raw data in type `[]byte`, a file name with type `string` or `io.ReadCloser`. You can load **as many data sources as you want**. Passing other types will simply return an error.

```go
cfg, err := ini.Load(
    []byte("raw data"), // Raw data
    "filename",         // File
    ioutil.NopCloser(bytes.NewReader([]byte("some other data"))),
)
```

Or start with an empty object:

```go
cfg := ini.Empty()
```

When you cannot decide how many data sources to load at the beginning, you will still be able to [Append()](https://gowalker.org/gopkg.in/ini.v1#File_Append) them later.

```go
err := cfg.Append("other file", []byte("other raw data"))
```

If you have a list of files with possibilities that some of them may not available at the time, and you don't know exactly which ones, you can use [LooseLoad()](https://gowalker.org/gopkg.in/ini.v1#LooseLoad) to ignore nonexistent files without returning error.

```go
cfg, err := ini.LooseLoad("filename", "filename_404")
```

The cool thing is, whenever the file is available to load while you're calling [Reload()](https://gowalker.org/gopkg.in/ini.v1#File_Reload) method, it will be counted as usual.

### Data Overwrite

One minor thing you need to keep in mind before you go. Data overwrite will happen when you load from more than one data sources. The value with same key name in the later data source will overwrite whatever previously read.

For example, if you load two files `my.ini` and `my.ini.local` (example input and output from [<i class="far fa-file-alt"></i> Getting Started](../intro/getting_started)), value of `app_mode` will be `production` not `development`.

```go
cfg, err := ini.Load("my.ini", "my.ini.local")
...

cfg.Section("").Key("app_mode").String() // production
```

The only exception to data overwrite rule is when you use [ShadowLoad](./work_with_keys#same-key-with-multiple-values).

### Skip Unrecognizable Lines

In the case that it is possible you do not have control over the input of the configuration and you do not care about lines that do not conform key-value pairs, you can tell parser to simply ignore them and continue parsing on the rest of the configuration:

```go
cfg, err := ini.LoadSources(ini.LoadOptions{
    SkipUnrecognizableLines: true,
}, "other.ini")
```

### Save your configuration

Finally, it's time to save your configuration to somewhere.

A typical way to save configuration is writing it to a file:

```go
// ...
err = cfg.SaveTo("my.ini")
err = cfg.SaveToIndent("my.ini", "\t")
```

Another way to save is writing to a `io.Writer` interface:

```go
// ...
cfg.WriteTo(writer)
cfg.WriteToIndent(writer, "\t")
```

By default, spaces are used to align "=" sign between key and values, to disable that:

```go
ini.PrettyFormat = false
```
