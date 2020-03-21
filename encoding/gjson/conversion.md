[TOC]


# 数据格式转换

数据格式转换有很多方法，具体请查看接口文档：https://godoc.org/github.com/gogf/gf/encoding/gjson

这里需要注意的是，有一些`Must*`转换方法，这些方法保证必须转换为指定的数据格式，否则直接`panic`。

## 常见数据格式转换

我们就来一个例子说明即可。

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
    fmt.Println("JSON:")
    fmt.Println(j.MustToJsonString())
    fmt.Println("======================")

    fmt.Println("XML:")
    fmt.Println(j.MustToXmlString())
    fmt.Println("======================")

    fmt.Println("YAML:")
    fmt.Println(j.MustToYamlString())
    fmt.Println("======================")

    fmt.Println("TOML:")
    fmt.Println(j.MustToTomlString())
}

// Output:
// JSON:
// {"users":{"array":["John","Ming"],"count":1}}
// ======================
// XML:
// <users><array>John</array><array>Ming</array><count>1</count></users>
// ======================
// YAML:
// users:
//     array:
//       - John
//       - Ming
//     count: 1
//
// ======================
// TOML:
// [users]
//   array = ["John", "Ming"]
//   count = 1.0
```
`gjson`支持将`JSON`转换为其他常见的数据格式，目前支持：`JSON`、`XML`、`INI`、`YAML/YML`、`TOML`、`Struct`数据格式之间的相互转换。


## `Strcut`对象转换

### `GetStruct`转换

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

### `ToStruct`转换

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






