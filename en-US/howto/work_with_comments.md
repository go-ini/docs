---
name: Work with comments
---

## Working with comments

Take care that following format will be treated as comment:

1. Line begins with `#` or `;`
2. Words after `#` or `;`
3. Words after section name (i.e words after `[some section name]`)

If you want to save a value with `#` or `;`, please quote them with ``` ` ``` or ``` """ ```.

Alternatively, you can use following [LoadOptions](https://gowalker.org/gopkg.in/ini.v1#LoadOptions) to completely ignore inline comments:

```go
cfg, err := ini.LoadSources(ini.LoadOptions{
    IgnoreInlineComment: true,
}, "app.ini")
```

Or require a space before the comment symbol:

```go
cfg, err := ini.LoadSources(ini.LoadOptions{
    SpaceBeforeInlineComment: true,
}, "app.ini")
```