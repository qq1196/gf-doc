[TOC]

# 日志级别

日志级别用于管理日志的输出，我们可以通过设定特定的日志级别来关闭/开启特定的日志内容。
日志级别的设置可以通过两个方法实现：
```go
func (l *Logger) SetLevel(level int)
func (l *Logger) SetLevelStr(levelStr string) error
```


## `SetLevel`方法
通过`SetLevel`方法可以设置日志级别，`glog`模块支持以下几种日志级别常量设定：
```go
LEVEL_ALL
LEVEL_DEV
LEVEL_PROD
LEVEL_DEBU 
LEVEL_INFO
LEVEL_NOTI
LEVEL_WARN
LEVEL_ERRO
LEVEL_CRIT
```
我们可以通过`位操作`组合使用这几种级别，例如其中`LEVEL_ALL`等价于`LEVEL_DEBU | LEVEL_INFO | LEVEL_NOTI | LEVEL_WARN | LEVEL_ERRO | LEVEL_CRIT`。我们还可以通过`LEVEL_ALL & ^LEVEL_DEBU & ^LEVEL_INFO & ^LEVEL_NOTI`来过滤掉`LEVEL_DEBU/LEVEL_INFO/LEVEL_NOTI`日志内容。

> 当然`glog`模块还有其他的一些级别，如`CRIT`和`FATA`，但是这两个级别是非常严重的错误，无法由开发者自定义屏蔽，产生严重错误的时候。将会产生一些额外的系统动作，如`panic`/`exit`。


使用示例：

```go
package main

import (
    "github.com/gogf/gf/os/glog"
)

func main() {
    l := glog.New()
    l.Info("info1")
    l.SetLevel(glog.LEVEL_ALL^glog.LEVEL_INFO)
    l.Info("info2")
}
```
执行后，输出结果为：
```html
2018-10-10 14:38:48.687 [INFO] info1
```
## `SetLevelStr`方法

大部分场景下我们可以通过`SetLevelStr`方法来通过字符串设置日志级别，配置文件中的`level`配置项也是通过字符串来配置日志级别。支持的日志级别字符串如下，不区分大小写：
```go
"ALL":      LEVEL_DEBU | LEVEL_INFO | LEVEL_NOTI | LEVEL_WARN | LEVEL_ERRO | LEVEL_CRIT,
"DEV":      LEVEL_DEBU | LEVEL_INFO | LEVEL_NOTI | LEVEL_WARN | LEVEL_ERRO | LEVEL_CRIT,
"DEVELOP":  LEVEL_DEBU | LEVEL_INFO | LEVEL_NOTI | LEVEL_WARN | LEVEL_ERRO | LEVEL_CRIT,
"PROD":     LEVEL_WARN | LEVEL_ERRO | LEVEL_CRIT,
"PRODUCT":  LEVEL_WARN | LEVEL_ERRO | LEVEL_CRIT,
"DEBU":     LEVEL_DEBU | LEVEL_INFO | LEVEL_NOTI | LEVEL_WARN | LEVEL_ERRO | LEVEL_CRIT,
"DEBUG":    LEVEL_DEBU | LEVEL_INFO | LEVEL_NOTI | LEVEL_WARN | LEVEL_ERRO | LEVEL_CRIT,
"INFO":     LEVEL_INFO | LEVEL_NOTI | LEVEL_WARN | LEVEL_ERRO | LEVEL_CRIT,
"NOTI":     LEVEL_NOTI | LEVEL_WARN | LEVEL_ERRO | LEVEL_CRIT,
"NOTICE":   LEVEL_NOTI | LEVEL_WARN | LEVEL_ERRO | LEVEL_CRIT,
"WARN":     LEVEL_WARN | LEVEL_ERRO | LEVEL_CRIT,
"WARNING":  LEVEL_WARN | LEVEL_ERRO | LEVEL_CRIT,
"ERRO":     LEVEL_ERRO | LEVEL_CRIT,
"ERROR":    LEVEL_ERRO | LEVEL_CRIT,
"CRIT":     LEVEL_CRIT,
"CRITICAL": LEVEL_CRIT,
```
可以看到，通过级别名称设置的日志级别是按照日志级别的高低来进行过滤的：`DEBU` < `INFO` < `NOTI` < `WARN` < `ERRO` < `CRIT`，也支持`ALL`, `DEV`, `PROD`常见部署模式配置名称。

使用示例：


```go
package main

import (
    "github.com/gogf/gf/os/glog"
)

func main() {
    l := glog.New()
    l.Info("info1")
    l.SetLevelStr("notice")
    l.Info("info2")
}
```
执行后，输出结果为：
```html
2018-10-10 14:38:48.687 [INFO] info1
```
