[TOC]


# 层级访问

`gjson`支持对数据内容进行层级检索访问，层级分隔符号默认为"`.`"。该特性使得开发者可以灵活访问未知的数据结构内容变得非常简便。

## 示例1，基本使用
```go
data :=
    `{
    "users" : {
        "count" : 2,
        "list"  : [
            {"name" : "Ming", "score" : 60},
            {"name" : "John", "score" : 99.5}
        ]
    }
}`
if j, err := gjson.DecodeToJson(data); err != nil {
    panic(err)
} else {
    fmt.Println("John Score:", j.GetFloat32("users.list.1.score"))
}
// Output:
// John Score: 99.5
```

可以看到，`gjson.Json`对象可以通过非常灵活的层级筛选功能(`j.GetFloat32("users.list.1.score")`)检索到对应的变量信息。

## 示例2，自定义层级分隔符号
```go
data :=
    `{
    "users" : {
        "count" : 2,
        "list"  : [
            {"name" : "Ming", "score" : 60},
            {"name" : "John", "score" : 99.5}
        ]
    }
}`
if j, err := gjson.DecodeToJson(data); err != nil {
    panic(err)
} else {
    j.SetSplitChar('#')
    fmt.Println("John Score:", j.GetFloat32("users#list#1#score"))
}
// Output:
// John Score: 99.5
```
可以看到，我们可以通过`SetSplitChar`方法设置我们自定义的分隔符号。
    
## 示例3，处理键名本身带有层级符号"`.`"的情况
```go
data :=
    `{
        "users" : {
            "count" : 100
        },
        "users.count" : 101
    }`
if j, err := gjson.DecodeToJson(data); err != nil {
    glog.Error(err)
} else {
    j.SetViolenceCheck(true)
    fmt.Println("Users Count:", j.GetInt("users.count"))
}
```
运行之后打印出的结果为`101`。当键名存在"`.`"号时，我们可以通过`SetViolenceCheck`设置冲突检测，随后检索优先级将会按照：键名->层级，便并不会引起歧义。但是当冲突检测开关开启时，检索效率将会变低，默认为关闭状态。