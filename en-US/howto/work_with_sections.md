---
name: Work with sections
---

## Working with sections

To get a section, you would need to:

```go
sec, err := cfg.GetSection("section name")
```

For a shortcut for default section, just give an empty string as name:

```go
sec, err := cfg.GetSection("")
```

Alternatively, you can use `ini.DEFAULT_SECTION` as section name to access the default section:

```go
sec, err := cfg.GetSection(ini.DEFAULT_SECTION)
```

When you're pretty sure the section exists, following code could make your life easier:

```go
sec := cfg.Section("section name")
```

What happens when the section somehow does not exist? Don't panic, it automatically creates and returns a new section to you.

To create a new section:

```go
err := cfg.NewSection("new section")
```

To get a list of sections or section names:

```go
secs := cfg.Sections()
names := cfg.SectionStrings()
```

### Parent-child Sections

You can use `.` in section name to indicate parent-child relationship between two or more sections. If the key not found in the child section, library will try again on its parent section until there is no parent section.

```ini
NAME = ini
VERSION = v1
IMPORT_PATH = gopkg.in/%(NAME)s.%(VERSION)s

[package]
CLONE_URL = https://%(IMPORT_PATH)s

[package.sub]
```

```go
cfg.Section("package.sub").Key("CLONE_URL").String()	// https://gopkg.in/ini.v1
```

### Unparseable Sections

Sometimes, you have sections that do not contain key-value pairs but raw content, to handle such case, you can use `LoadOptions.UnparsableSections`:

```go
cfg, err := ini.LoadSources(ini.LoadOptions{
    UnparseableSections: []string{"COMMENTS"},
}, `[COMMENTS]
<1><L.Slide#2> This slide has the fuel listed in the wrong units <e.1>`)

body := cfg.Section("COMMENTS").Body()

/* --- start ---
<1><L.Slide#2> This slide has the fuel listed in the wrong units <e.1>
------  end  --- */
```

### Multiple sections with the same name

By default, a section with the same name as a previous section will override the latter. To enable loading of non-unique sections, `LoadOptions.AllowNonUniqueSections` must be enabled:

```go
cfg, err := ini.LoadSources(
	ini.LoadOptions{
		AllowNonUniqueSections: true,
	},
	`
[system]
foo = bar

[system]
baz = gazonk
`,
)
```
