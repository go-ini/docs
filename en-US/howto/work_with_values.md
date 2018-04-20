---
name: Work with values
---

## Working with values

To get a string value:

```go
val := cfg.Section("").Key("key name").String()
```

To validate key value on the fly:

```go
val := cfg.Section("").Key("key name").Validate(func(in string) string {
	if len(in) == 0 {
		return "default"
	}
	return in
})
```

If you do not want any auto-transformation (such as recursive read) for the values, you can get raw value directly (this way you get much better performance):

```go
val := cfg.Section("").Key("key name").Value()
```

To check if raw value exists:

```go
yes := cfg.Section("").HasValue("test value")
```

To get value with types:

```go
// For boolean values:
// true when value is: 1, t, T, TRUE, true, True, YES, yes, Yes, y, ON, on, On
// false when value is: 0, f, F, FALSE, false, False, NO, no, No, n, OFF, off, Off
v, err = cfg.Section("").Key("BOOL").Bool()
v, err = cfg.Section("").Key("FLOAT64").Float64()
v, err = cfg.Section("").Key("INT").Int()
v, err = cfg.Section("").Key("INT64").Int64()
v, err = cfg.Section("").Key("UINT").Uint()
v, err = cfg.Section("").Key("UINT64").Uint64()
v, err = cfg.Section("").Key("TIME").TimeFormat(time.RFC3339)
v, err = cfg.Section("").Key("TIME").Time() // RFC3339

v = cfg.Section("").Key("BOOL").MustBool()
v = cfg.Section("").Key("FLOAT64").MustFloat64()
v = cfg.Section("").Key("INT").MustInt()
v = cfg.Section("").Key("INT64").MustInt64()
v = cfg.Section("").Key("UINT").MustUint()
v = cfg.Section("").Key("UINT64").MustUint64()
v = cfg.Section("").Key("TIME").MustTimeFormat(time.RFC3339)
v = cfg.Section("").Key("TIME").MustTime() // RFC3339

// Methods start with Must also accept one argument for default value
// when key not found or fail to parse value to given type.
// Except method MustString, which you have to pass a default value.

v = cfg.Section("").Key("String").MustString("default")
v = cfg.Section("").Key("BOOL").MustBool(true)
v = cfg.Section("").Key("FLOAT64").MustFloat64(1.25)
v = cfg.Section("").Key("INT").MustInt(10)
v = cfg.Section("").Key("INT64").MustInt64(99)
v = cfg.Section("").Key("UINT").MustUint(3)
v = cfg.Section("").Key("UINT64").MustUint64(6)
v = cfg.Section("").Key("TIME").MustTimeFormat(time.RFC3339, time.Now())
v = cfg.Section("").Key("TIME").MustTime(time.Now()) // RFC3339
```

What if my value is three-line long?

```ini
[advance]
ADDRESS = """404 road,
NotFound, State, 5000
Earth"""
```

Not a problem!

```go
cfg.Section("advance").Key("ADDRESS").String()

/* --- start ---
404 road,
NotFound, State, 5000
Earth
------  end  --- */
```

That's cool, how about continuation lines?

```ini
[advance]
two_lines = how about \
	continuation lines?
lots_of_lines = 1 \
	2 \
	3 \
	4
```

Piece of cake!

```go
cfg.Section("advance").Key("two_lines").String() // how about continuation lines?
cfg.Section("advance").Key("lots_of_lines").String() // 1 2 3 4
```

Well, I hate continuation lines, how do I disable that?

```go
cfg, err := ini.LoadSources(ini.LoadOptions{
	IgnoreContinuation: true,
}, "filename")
```

Holy crap!

Note that single quotes around values will be stripped:

```ini
foo = "some value" // foo: some value
bar = 'some value' // bar: some value
```

Sometimes you downloaded file from [Crowdin](https://crowdin.com/) has values like the following (value is surrounded by double quotes and quotes in the value are escaped):

```ini
create_repo="created repository <a href=\"%s\">%s</a>"
```

How do you transform this to regular format automatically?

```go
cfg, err := ini.LoadSources(ini.LoadOptions{UnescapeValueDoubleQuotes: true}, "en-US.ini"))
cfg.Section("<name of your section>").Key("create_repo").String()
// You got: created repository <a href="%s">%s</a>
```

That's all? Hmm, no.

#### Helper methods of working with values

To get value with given candidates:

```go
v = cfg.Section("").Key("STRING").In("default", []string{"str", "arr", "types"})
v = cfg.Section("").Key("FLOAT64").InFloat64(1.1, []float64{1.25, 2.5, 3.75})
v = cfg.Section("").Key("INT").InInt(5, []int{10, 20, 30})
v = cfg.Section("").Key("INT64").InInt64(10, []int64{10, 20, 30})
v = cfg.Section("").Key("UINT").InUint(4, []int{3, 6, 9})
v = cfg.Section("").Key("UINT64").InUint64(8, []int64{3, 6, 9})
v = cfg.Section("").Key("TIME").InTimeFormat(time.RFC3339, time.Now(), []time.Time{time1, time2, time3})
v = cfg.Section("").Key("TIME").InTime(time.Now(), []time.Time{time1, time2, time3}) // RFC3339
```

Default value will be presented if value of key is not in candidates you given, and default value does not need be one of candidates.

To validate value in a given range:

```go
vals = cfg.Section("").Key("FLOAT64").RangeFloat64(0.0, 1.1, 2.2)
vals = cfg.Section("").Key("INT").RangeInt(0, 10, 20)
vals = cfg.Section("").Key("INT64").RangeInt64(0, 10, 20)
vals = cfg.Section("").Key("UINT").RangeUint(0, 3, 9)
vals = cfg.Section("").Key("UINT64").RangeUint64(0, 3, 9)
vals = cfg.Section("").Key("TIME").RangeTimeFormat(time.RFC3339, time.Now(), minTime, maxTime)
vals = cfg.Section("").Key("TIME").RangeTime(time.Now(), minTime, maxTime) // RFC3339
```

##### Auto-split values into a slice

To use zero value of type for invalid inputs:

```go
// Input: 1.1, 2.2, 3.3, 4.4 -> [1.1 2.2 3.3 4.4]
// Input: how, 2.2, are, you -> [0.0 2.2 0.0 0.0]
vals = cfg.Section("").Key("STRINGS").Strings(",")
vals = cfg.Section("").Key("FLOAT64S").Float64s(",")
vals = cfg.Section("").Key("INTS").Ints(",")
vals = cfg.Section("").Key("INT64S").Int64s(",")
vals = cfg.Section("").Key("UINTS").Uints(",")
vals = cfg.Section("").Key("UINT64S").Uint64s(",")
vals = cfg.Section("").Key("TIMES").Times(",")
```

To exclude invalid values out of result slice:

```go
// Input: 1.1, 2.2, 3.3, 4.4 -> [1.1 2.2 3.3 4.4]
// Input: how, 2.2, are, you -> [2.2]
vals = cfg.Section("").Key("FLOAT64S").ValidFloat64s(",")
vals = cfg.Section("").Key("INTS").ValidInts(",")
vals = cfg.Section("").Key("INT64S").ValidInt64s(",")
vals = cfg.Section("").Key("UINTS").ValidUints(",")
vals = cfg.Section("").Key("UINT64S").ValidUint64s(",")
vals = cfg.Section("").Key("TIMES").ValidTimes(",")
```

Or to return nothing but error when have invalid inputs:

```go
// Input: 1.1, 2.2, 3.3, 4.4 -> [1.1 2.2 3.3 4.4]
// Input: how, 2.2, are, you -> error
vals = cfg.Section("").Key("FLOAT64S").StrictFloat64s(",")
vals = cfg.Section("").Key("INTS").StrictInts(",")
vals = cfg.Section("").Key("INT64S").StrictInt64s(",")
vals = cfg.Section("").Key("UINTS").StrictUints(",")
vals = cfg.Section("").Key("UINT64S").StrictUint64s(",")
vals = cfg.Section("").Key("TIMES").StrictTimes(",")
```

### Recursive Values

For all value of keys, there is a special syntax `%(<name>)s`, where `<name>` is the key name in same section or default section, and `%(<name>)s` will be replaced by corresponding value(empty string if key not found). You can use this syntax at most 99 level of recursions.

```ini
NAME = ini

[author]
NAME = Unknwon
GITHUB = https://github.com/%(NAME)s

[package]
FULL_NAME = github.com/go-ini/%(NAME)s
```

```go
cfg.Section("author").Key("GITHUB").String()		// https://github.com/Unknwon
cfg.Section("package").Key("FULL_NAME").String()	// github.com/go-ini/ini
```

### Python Multiline values

In case, you migrate service from Python and has some legacy configurations, don't panic!

```go
cfg, err := ini.LoadSources(ini.LoadOptions{
    AllowPythonMultilineValues: true,
}, []byte(`
[long]
long_rsa_private_key = -----BEGIN RSA PRIVATE KEY-----
   foo
   bar
   foobar
   barfoo
   -----END RSA PRIVATE KEY-----
`)

/*
-----BEGIN RSA PRIVATE KEY-----
foo
bar
foobar
barfoo 
-----END RSA PRIVATE KEY-----
*/
```