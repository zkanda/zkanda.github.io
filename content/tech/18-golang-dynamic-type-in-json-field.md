+++
author = "Zakatell Kanda"
date = 2018-11-07T04:19:05Z
description = ""
draft = false
slug = "golang-dynamic-type-in-json-field"
title = "golang: dynamic type in a json field"

+++

We had an experience where the type of a field change base on certain conditions. We think this is bad, but we have to support it. Here's how.

You'll have to specify the field type as an interface.

```go
type P struct {
    T interface{} `json:"t"`
}
```

After that, you'll need to use `switch` to determine the type. In our case, we needed it as int.

```go
var i int

var p P

s := `{"t": "3460"}`
// should also work with
// s := `{"t": 3460}`
if err := json.NewDecoder(r.Body).Decode(strings.NewReader(s)); err != nil {
    fmt.Errorln(err)
}

switch t := p.(type) {
case float64:
    i := int(t)
case string:
    var err error
    if i, err = strconv.Atoi(t); err != nil {
        return nil, fmt.Errorf("t is not a number: %#v", err)
    }
default:
    fmt.Errorf("t is of invalid type: %#v", t)
}
```

Simple as that. Thanks again to my colleague Harmen for this solution.
