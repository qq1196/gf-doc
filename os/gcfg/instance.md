[TOC]

# 单例对象

`gcfg`集成到了`gf`框架的单例管理器中，可以方便地通过`g.Cfg()`获取默认的全局配置管理对象。同时，我们也可以通过`gcfg.Instance`包方法获取配置管理对象单例。

## 使用`g.Config`
我们来看一个示例，演示如何读取全局配置的信息。需要注意的是，全局配置是与框架相关的，因此统一使用`g.Cfg()`进行获取。以下是一个默认的全局配置文件，包含了模板引擎的目录配置以及MySQL数据库集群（两台master）的配置。

```go
package main

import (
    "fmt"
    "github.com/gogf/gf/frame/g"
)

func main() {
    fmt.Println(g.Cfg().GetString("viewpath"))
    fmt.Println(g.Cfg().GetString("database.default.0.host"))
}
```
以上示例为读取数据库的第一个配置的host信息。运行后输出：
```html
/home/www/templates/
127.0.0.1
```
可以看到，我们可以通过`g.Cfg()`方法获取一个全局的配置管理器单例对象。配置文件内容可以通过英文“`.`”号进行层级访问（数组默认从0开始），`pattern`参数`database.default.0.host`表示读取`database`配置项中`default`数据库集群中的第`0`项数据库服务器的`host`数据。

## 使用`gcfg.Instance`

当然也可以独立使用`gcfg`包，通过`Instance`方法获取单例对象。

```go
package main

import (
	"fmt"
	"github.com/gogf/gf/os/gcfg"
)

func main() {
	fmt.Println(gcfg.Instance().GetString("viewpath"))
	fmt.Println(gcfg.Instance().GetString("database.default.0.host"))
}
```

## 注意事项

从`GF v1.12`版本开始，为方便多文件场景下的配置文件调用，简便使用并提高开发效率，因此当给定的单例名称对应的`toml`配置文件在配置目录中存在时，将自动设置该单例对象的默认配置文件为该文件。例如：`g.Cfg("redis")`获取到的单例对象将会默认去检索并设置默认的配置文件为`redis.toml`，当该文件不存在时，则使用默认的配置文件（`config.toml`）。






















