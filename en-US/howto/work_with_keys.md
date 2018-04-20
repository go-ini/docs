---
name: Work with keys
---

## Working with keys

To get a key under a section:

```go
key, err := cfg.Section("").GetKey("key name")
```

Same rule applies to key operations:

```go
key := cfg.Section("").Key("key name")
```

To check if a key exists:

```go
yes := cfg.Section("").HasKey("key name")
```

To create a new key:

```go
err := cfg.Section("").NewKey("name", "value")
```

To get a list of keys or key names:

```go
keys := cfg.Section("").Keys()
names := cfg.Section("").KeyStrings()
```

To get a clone hash of keys and corresponding values:

```go
hash := cfg.Section("").KeysHash()
```

#### Ignore cases of key name

When you do not care about cases of section and key names, you can use [InsensitiveLoad](https://gowalker.org/gopkg.in/ini.v1#InsensitiveLoad) to force all names to be lowercased while parsing.

```go
cfg, err := ini.InsensitiveLoad("filename")
//...

// sec1 and sec2 are the exactly same section object
sec1, err := cfg.GetSection("Section")
sec2, err := cfg.GetSection("SecTIOn")

// key1 and key2 are the exactly same key object
key1, err := sec1.GetKey("Key")
key2, err := sec2.GetKey("KeY")
```

#### MySQL-like boolean key

MySQL's configuration allows a key without value as follows:

```ini
[mysqld]
...
skip-host-cache
skip-name-resolve
```

By default, this is considered as missing value. But if you know you're going to deal with those cases, you can assign advanced load options:

```go
cfg, err := ini.LoadSources(ini.LoadOptions{
    AllowBooleanKeys: true,
}, "my.cnf")
```

The value of those keys are always `true`, and when you save to a file, it will keep in the same foramt as you read.

To generate such keys in your program, you could use `NewBooleanKey`:

```go
key, err := sec.NewBooleanKey("skip-host-cache")
```

### Same Key with Multiple Values

Do you ever have a configuration file like this?

```ini
[remote "origin"]
url = https://github.com/Antergone/test1.git
url = https://github.com/Antergone/test2.git
fetch = +refs/heads/*:refs/remotes/origin/*
```

By default, only the last read value will be kept for the key `url`. If you want to keep all copies of value of this key, you can use [ShadowLoad](https://gowalker.org/gopkg.in/ini.v1#ShadowLoad) to achieve it:

```go
cfg, err := ini.ShadowLoad(".gitconfig")
// ...

f.Section(`remote "origin"`).Key("url").String() 
// Result: https://github.com/Antergone/test1.git

f.Section(`remote "origin"`).Key("url").ValueWithShadows()
// Result:  []string{
//              "https://github.com/Antergone/test1.git",
//              "https://github.com/Antergone/test2.git",
//          }
```

### Auto-increment Key Names

If key name is `-` in data source, then it would be seen as special syntax for auto-increment key name start from 1, and every section is independent on counter.

```ini
[features]
-: Support read/write comments of keys and sections
-: Support auto-increment of key names
-: Support load multiple files to overwrite key values
```

```go
cfg.Section("features").KeyStrings()	// []{"#1", "#2", "#3"}
```

### Retrieve parent keys available to a child section

```go
cfg.Section("package.sub").ParentKeys() // ["CLONE_URL"]
```