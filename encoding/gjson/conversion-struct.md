[TOC]


# `Strcut`对象转换

## `ToStruct`转换

`To*`方法用于将整个`Json`包含的数据内容转换为指定的数据格式或者对象。

```go
data :=
    `
{
    "count" : 1,
    "array" : ["John", "Ming"]
}`
if j, err := gjson.DecodeToJson(data); err != nil {
    panic(err)
} else {
    type Users struct {
        Count int
        Array []string
    }
    users := new(Users)
    if err := j.ToStruct(users); err != nil {
        panic(err)
    }
    fmt.Printf(`%+v`, users)
}

// Output:
// &{Count:1 Array:[John Ming]}
```

## `GetStruct`转换

`Get*`方法用于获得指定层级的节点数据，并将该数据转换为指定的结构体对象。

```go
data :=
    `{
    "users" : {
        "count" : 1,
        "array" : ["John", "Ming"]
    }
}`
if j, err := gjson.DecodeToJson(data); err != nil {
    panic(err)
} else {
    type Users struct {
        Count int
        Array []string
    }
    users := new(Users)
    if err := j.GetStruct("users", users); err != nil {
        panic(err)
    }
    fmt.Printf(`%+v`, users)
}

// Output:
// &{Count:1 Array:[John Ming]}
```








