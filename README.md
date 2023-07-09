# teasy
## Template Easy

### Sample:
```go
symbols := teasy.SymbolMap{
    "obj":         &Object{Value1: "abc"},
    "add":         add,
    "count":       count,
    "dddArg":      dddArg,
    "die":         func() bool { panic("die") },
    "echo":        echo,
    "makemap":     makemap,
    "mapOfThree":  mapOfThree,
    "oneArg":      oneArg,
    "returnInt":   returnInt,
    "stringer":    stringer,
    "twoArgs":     twoArgs,
    "typeOf":      typeOf,
    "valueString": valueString,
    "vfunc":       vfunc,
    "zeroArgs":    zeroArgs,
    "lower":       strings.ToLower,
    "upper":       strings.ToUpper,
    "repeat":      strings.Repeat,
    "replace":     strings.Replace,
    "replaceAll":  strings.ReplaceAll,
    "concat": func(a ...string) string {
        sb := strings.Builder{}
        for _, s := range a {
            sb.WriteString(s)
        }
        return sb.String()
    },
}

tmpl, err := teasy.New("template").Symbols(symbols).Parse(text)
if err != nil {
    return "", fmt.Errorf(`error: %s while executing "%s"`, err.Error(), tmpl.Name())
}
b := new(strings.Builder)
err = tmpl.Execute(b, data)
if err != nil {
    return "", err
}
return b.String(), nil

```
### Features

1. Use parentheses as a pipeline(function, call)
```go
text = `{{ concat(repeat("a", 3), "b") | upper}}` // AAAB
```
2. Use the `?` as a placeholder for an input variable passed from the pipeline
```go
text = `{{ "abc"| concat(?,?) | upper}}` // ABCABC
text = "{{ .NonEmptyInterface | print(? | ?.X) | ? }}" // x
```
be careful
  * The support for automatically adding the pipeline variable to the call arguments has been removed.
```go
text = `{{ "abc"| concat("cde") | upper}}` // CDE
text = `{{ "abc"| concat("cde", ?) | upper}}` // CDEABC
```

  * The pipeline variable will be automatically appended to the call arguments only when no argument is supplied.
```go
text = `{{ "abc"| concat | upper }}` // ABC
text = `{{ "abc"| concat ? | upper ? }}` // ABC
text = `{{ "abc"| concat(?) | upper(?) }}` // ABC
```

3. Replace `Funcs` with `Symbols`, you can use variables, functions
```go
type Object struct {
  Value1 string
}
func (o *Object) GetVal1() string {
  return o.Value1
}

symbols := SymbolMap{
    "obj":         &Object{Value1: "abc"},
    "add":         add,
    "count":       count,
    "dddArg":      dddArg,
    "die":         func() bool { panic("die") },
    "echo":        echo,
    "makemap":     makemap,
    "mapOfThree":  mapOfThree,
    "oneArg":      oneArg,
    "returnInt":   returnInt,
    "stringer":    stringer,
    "twoArgs":     twoArgs,
    "typeOf":      typeOf,
    "valueString": valueString,
    "vfunc":       vfunc,
    "zeroArgs":    zeroArgs,
    "lower":       strings.ToLower,
    "upper":       strings.ToUpper,
    "repeat":      strings.Repeat,
    "replace":     strings.Replace,
    "replaceAll":  strings.ReplaceAll,
    "concat": func(a ...string) string {
        sb := strings.Builder{}
        for _, s := range a {
            sb.WriteString(s)
        }
        return sb.String()
    },
}
text = `{{ obj.GetVal1() }}` // abc
text = `{{ obj.Value1 }}` // abc
```
