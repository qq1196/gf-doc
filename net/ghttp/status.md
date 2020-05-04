

我们可以对WebServer指定的状态码进行自定义处理，例如针对常见的```404/403/500```等错误，我们可以展示自定义的错误信息、页面内容，或者跳转到一个特定的页面。

相关方法如下：
```go
func (s *Server) BindStatusHandler(status int, handler HandlerFunc)
func (s *Server) BindStatusHandlerByMap(handlerMap map[int]HandlerFunc)

func (d *Domain) BindStatusHandler(status int, handler HandlerFunc)
func (d *Domain) BindStatusHandlerByMap(handlerMap map[int]HandlerFunc)
```
可以看到，我们可以使用```BindStatusHandler```或者```BindStatusHandlerMap```来实现针对指定的状态码进行自定义的回调函数处理，并且该特性也支持针对特定的域名绑定。我们来看几个简单的示例。

# 基本使用

```go
package main

import (
    "github.com/gogf/gf/frame/g"
    "github.com/gogf/gf/net/ghttp"
)

func main() {
    s := g.Server()
    s.BindHandler("/", func(r *ghttp.Request){
        r.Response.Writeln("halo 世界！")
    })
    s.BindStatusHandler(404, func(r *ghttp.Request){
        r.Response.WriteOver("This is customized 404 page")
    })
    s.SetPort(8199)
    s.Run()
}
```
执行后，我们访问没有绑定的路由页面，例如 http://127.0.0.1:8199/test ，可以看到，页面显示了我们期望的返回结果：```This is customized 404 page```。

此外，常见的Web页面请求错误状态码处理方式，是引导用户跳转到指定的错误页面，因此，在状态码回调处理函数中，我们可以使用```r.RedirectTo```方法来进行页面跳转，示例如下：

```go
package main

import (
    "github.com/gogf/gf/frame/g"
    "github.com/gogf/gf/net/ghttp"
)

func main() {
    s := g.Server()
    s.BindHandler("/status/:status", func(r *ghttp.Request) {
        r.Response.Write("woops, status ", r.Get("status"), " found")
    })
    s.BindStatusHandler(404, func(r *ghttp.Request){
        r.Response.RedirectTo("/status/404")
    })
    s.SetPort(8199)
    s.Run()
}
```
执行后，我们手动通过浏览器访问一个不存在的页面，例如 http://127.0.0.1:8199/test ，可以看到，页面被引导跳转到了 http://127.0.0.1:8199/status/404 页面，并且可以看到页面返回内容：```woops, status 404 found```


# 批量设置

```go
package main

import (
    "github.com/gogf/gf/frame/g"
    "github.com/gogf/gf/net/ghttp"
)

func main() {
    s := g.Server()
    s.BindStatusHandlerByMap(map[int]ghttp.HandlerFunc {
        403 : func(r *ghttp.Request){r.Response.WriteOver("403")},
        404 : func(r *ghttp.Request){r.Response.WriteOver("404")},
        500 : func(r *ghttp.Request){r.Response.WriteOver("500")},
    })
    s.SetPort(8199)
    s.Run()
}
```
可以看到，我们可以通过```BindStatusHandlerByMap```方法对需要自定义的状态码进行批量设置。该示例程序执行后，当服务接口返回的状态码为```403/404/500```时，接口将会返回对应的状态码数字。













