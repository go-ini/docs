---
name: Customize name and value mappers
---

### Name Mapper

To save your time and make your code cleaner, this library supports [`NameMapper`](https://gowalker.org/gopkg.in/ini.v1#NameMapper) between struct field and actual section and key name.

There are 2 built-in name mappers:

- `AllCapsUnderscore`: it converts to format `ALL_CAPS_UNDERSCORE` then match section or key.
- `TitleUnderscore`: it converts to format `title_underscore` then match section or key.

To use them:

```go
type Info struct {
	PackageName string
}

func main() {
	err = ini.MapToWithMapper(&Info{}, ini.TitleUnderscore, []byte("package_name=ini"))
	// ...

	cfg, err := ini.Load([]byte("PACKAGE_NAME=ini"))
	// ...
	info := new(Info)
	cfg.NameMapper = ini.AllCapsUnderscore
	err = cfg.MapTo(info)
	// ...
}
```

Same rules of name mapper apply to `ini.ReflectFromWithMapper` function.

### Value Mapper

To expand values (e.g. from environment variables), you can use the `ValueMapper` to transform values:

```go
type Env struct {
	Foo string `ini:"foo"`
}

func main() {
	cfg, err := ini.Load([]byte("[env]\nfoo = ${MY_VAR}\n"))
	cfg.ValueMapper = os.ExpandEnv
	// ...
	env := &Env{}
	err = cfg.Section("env").MapTo(env)
}
```

This would set the value of `env.Foo` to the value of the environment variable `MY_VAR`.
