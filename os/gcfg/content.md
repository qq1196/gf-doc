

# 内容配置

`gcfg`包也支持直接内容配置，而不是读取配置文件，常用于程序内部动态修改配置内容。

通过以下包配置方法实现全局的配置：
```go
func SetContent(content string, file ...string)
func GetContent(file ...string) string
func RemoveContent(file ...string)
func ClearContent()
```
需要注意的是该配置是全局生效的，并且优先级会高于读取配置文件。

因此，假如我们通过`gcfg.SetContent("v = 1", "config.toml")`配置了`config.toml`的配置内容，并且也同时存在`config.toml`配置文件，那么只会使用到`SetContent`的配置内容，而配置文件内容将会被忽略。






