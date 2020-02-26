# 自定义Header

http客户端发起请求时可以自定义发送给服务端的Header内容，该特性使用`SetHeader`/`SetHeaderRaw`方法实现。我们来看一个客户端自定义Cookie的例子。

## 服务端

https://github.com/gogf/gf/blob/master/.example/net/ghttp/client/cookie/server.go
```go
package main

import (
    "github.com/gogf/gf/frame/g"
    "github.com/gogf/gf/net/ghttp"
)

func main() {
    s := g.Server()
    s.BindHandler("/", func(r *ghttp.Request){
        r.Response.Writeln(r.Cookie.Map())
    })
    s.SetPort(8199)
    s.Run()
}
```
由于是作为示例，服务端的逻辑很简单，直接将接收到的`Cookie`参数全部返回给客户端。


## 客户端
    
1. 使用`SetHeader`方法
    https://github.com/gogf/gf/blob/master/.example/net/ghttp/client/cookie/client.go
    ```go
    package main

    import (
        "fmt"
        "github.com/gogf/gf/net/ghttp"
    )

    func main() {
        c := ghttp.NewClient()
        c.SetHeader("Cookie", "name=john; score=100")
        if r, e := c.Get("http://127.0.0.1:8199/"); e != nil {
            panic(e)
        } else {
            fmt.Println(r.ReadAllString())
        }
    }
    ```
    通过`ghttp.NewClient()`创建一个自定义的http请求客户端对象，并通过`c.SetHeader("Cookie", "name=john; score=100")`设置自定义的Cookie，这里我们设置了两个示例用的Cookie参数，一个`name`，一个`score`，注意多个Cookie参数使用`;`符号分隔。

1. 使用`SetHeaderRaw`方法

    这个方法更加简单，可以通过原始的Header字符串来设置客户端请求Header。
    ```go
    c := ghttp.NewClient()
    c.SetHeaderRaw(`
        accept-encoding: gzip, deflate, br
        accept-language: zh-CN,zh;q=0.9,en;q=0.8
        referer: https://idonottell.you
        cookie: name=john; score=100
        user-agent: my test http client
    `)
    if r, e := c.Get("http://127.0.0.1:8199/"); e != nil {
        panic(e)
    } else {
        fmt.Println(r.ReadAllString())
    }
    ```

1. 执行结果

	客户端代码执行后，终端将会打印出服务端的返回结果，如下：
    ```shell
    map[name:john score:100]
    ```
    可以看到，服务端已经接收到了客户端自定义的`Cookie`参数。

