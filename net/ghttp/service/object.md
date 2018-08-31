
[TOC]

# 执行对象注册

执行对象注册是在注册时便给定一个实例化的对象，以后每一个请求都交给该对象(同一对象)处理，该对象常驻内存不释放。由于相比较控制器注册来说，执行对象注册方式在处理请求的时候不需要不停地创建/销毁控制器对象，因此请求处理效率会高很多。

这种注册方式的缺点也很明显，服务端进程在启动时便需要初始化这些执行对象，并且这些对象需要自行负责对自身数据的并发安全维护。执行对象的定义没有严格要求，也没有强行要求继承```gmvc.Controller```控制器基类，因为在请求进入时没有自动初始化流程，内部的成员变量需要自行维护（包括变量初始化，变量销毁等）。


## 执行对象注册

我们可以通过```ghttp.BindObject```方法完成执行对象的注册。

[gitee.com/johng/gf/blob/master/geg/frame/mvc/controller/demo/object.go](https://gitee.com/johng/gf/blob/master/geg/frame/mvc/controller/demo/object.go)

```go
package demo

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
)

type Object struct {}

func init() {
    g.Server().BindObject("/object", new(Object))
}

func (o *Object) Index(r *ghttp.Request) {
    r.Response.Write("object index")
}

func (o *Object) Show(r *ghttp.Request) {
    r.Response.Write("object show")
}
```
可以看到，执行对象在进行服务注册时便生成了一个对象(执行对象在Web Server启动时便生成)，此后不管多少请求进入，Web Server都是将请求转交给该对象对应的方法进行处理。需要注意的是，公开方法的定义，必须为以下形式：
```go
func(r *ghttp.Request) 
```
否则无法完成注册，调用注册方法时会有错误提示，例如：
```shell
panic: interface conversion: interface {} is xxx, not func(*ghttp.Request)
```
该示例执行后可以通过，可以通过```http://127.0.0.1:8199/object/show```查看效果。

### 默认路由方法

控制器中的```Index```方法是一个特殊的方法，例如，当注册的路由规则为```/user```时，HTTP请求到```/user```时，将会自动映射到控制器的```Index```方法。也就是说，访问```/user```和```/user/index```将会达到相同的执行效果。对后续的控制器注册方式同理。

### 路由内置变量

对于控制器和执行对象的注册方式来讲，由于是基于对象来进行注册，因此在路由规则中可以使用内置的两个变量：```{.struct}```和```{.method}```，前者表示当前**对象名称**，后者表示当前注册的**方法名**。我们来看一个例子：
[gitee.com/johng/gf/blob/master/geg/frame/mvc/controller/demo/buildin-vars.go](https://gitee.com/johng/gf/blob/master/geg/frame/mvc/controller/demo/buildin-vars.go)
```go
package demo

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
)

type Order struct { }

func init() {
    g.Server().BindObject("/{.struct}-{.method}", new(Order))
}

func (o *Order) List(r *ghttp.Request) {
    r.Response.Write("List")
}
```
启动外层的```main.go```，我们尝试着访问```http://127.0.0.1:8199/order-list```，可以看到页面输出```List```。如果路由规则中不使用内置变量，那么默认的情况下，方法将会被追加到指定的路由规则末尾。

### 命名风格规则
当方法名称带有多个单词时，路由控制器默认会自动使用英文连接符号```-```进行拼接，因此访问的时候方法名称需要带```-```号。例如，方法名为```UserName```时，生成的URI地址为```user-name```；方法名为```ShowListItems```，生成的URI地址为```show-list-items```；以此类推。对后续的控制器注册方式同理。

此外，我们可以通过```ghttp.Server.SetNameToUriType```方法来设置struct/method名称与uri的转换方式。支持的方式目前有4种，对应4个常量定义：
```go
NAME_TO_URI_TYPE_DEFAULT  = 0      // 服务注册时对象和方法名称转换为URI时，全部转为小写，单词以'-'连接符号连接
NAME_TO_URI_TYPE_FULLNAME = 1      // 不处理名称，以原有名称构建成URI
NAME_TO_URI_TYPE_ALLLOWER = 2      // 仅转为小写，单词间不使用连接符号
NAME_TO_URI_TYPE_CAMEL    = 3      // 采用驼峰命名方式
```
我们来看一个示例：
[gitee.com/johng/gf/blob/master/geg/net/ghttp/server/name.go](https://gitee.com/johng/gf/blob/master/geg/net/ghttp/server/name.go)
```go
package main

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
)

type User struct {}

func (u *User) ShowList(r *ghttp.Request) {
    r.Response.Write("list")
}

func main() {
    s1 := g.Server(1)
    s2 := g.Server(2)
    s3 := g.Server(3)
    s4 := g.Server(4)

    s1.SetNameToUriType(ghttp.NAME_TO_URI_TYPE_DEFAULT)
    s2.SetNameToUriType(ghttp.NAME_TO_URI_TYPE_FULLNAME)
    s3.SetNameToUriType(ghttp.NAME_TO_URI_TYPE_ALLLOWER)
    s4.SetNameToUriType(ghttp.NAME_TO_URI_TYPE_CAMEL)

    s1.BindObject("/{.struct}/{.method}", new(User))
    s2.BindObject("/{.struct}/{.method}", new(User))
    s3.BindObject("/{.struct}/{.method}", new(User))
    s4.BindObject("/{.struct}/{.method}", new(User))

    s1.SetPort(8100)
    s2.SetPort(8200)
    s3.SetPort(8300)
    s4.SetPort(8400)

    s1.Start()
    s2.Start()
    s3.Start()
    s4.Start()

    g.Wait()
}
```
这个示例采用了多Server运行方式，将不同的名称转换方式使用了不同的Server来配置运行，因此我们可以方便地在同一个程序中，访问不同的Server（通过不同的端口绑定）看到不同的结果。执行以上示例后，可以分别访问以下URL地址得到期望的结果：
```html
http://127.0.0.1:8100/user/show-list
http://127.0.0.1:8200/User/ShowList
http://127.0.0.1:8300/user/showlist
http://127.0.0.1:8300/user/showList
```

### 对象方法注册

假如控制器中有若干公开方法，但是我只想注册其中几个，其余的方法我不想对外公开，怎么办？

我们可以通过```BindObject```传递第三个非必需参数替换实现，参数支持传入多个方法名称，多个名称以英文```,```号分隔（**方法名称参数区分大小写**）。

示例：
```go
package demo

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
)

type Object struct {}

func init() {
    obj := new(Object)
    g.Server().BindObject("/object", obj)
    g.Server().BindObjectMethod("/object-show", obj, "Show")
}

func (o *Object) Index(r *ghttp.Request) {
    r.Response.Write("object index")
}

func (o *Object) Show(r *ghttp.Request) {
    r.Response.Write("object show")
}
```


## 绑定路由方法


我们可以通过```BindObjectMethod```方法绑定路由到指定的方法执行（**方法名称参数区分大小写**）。

来看一个例子：

```go
package demo

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
)

type ObjectMethod struct {}

func init() {
    obj := &ObjectMethod{}
    g.Server().BindObjectMethod("/object-method-show1", obj, "Show1")
    g.Server().Domain("localhost").BindObjectMethod("/object-method-show2", obj, "Show2")
}

func (o *ObjectMethod) Show1(r *ghttp.Request) {
    r.Response.Write("show 1")
}

func (o *ObjectMethod) Show2(r *ghttp.Request) {
    r.Response.Write("show 2")
}

func (o *ObjectMethod) Show3(r *ghttp.Request) {
    r.Response.Write("show 3")
}
```
这个例子比较简单，并且也演示了域名绑定路由到执行对象的指定方法负责执行。


## RESTful对象注册

```RESTful```设计方式的控制器，通常用于API服务。**在这种模式下，HTTP的Method将会映射到控制器对应的方法名称**，例如：```POST```方式将会映射到控制器的```Post```方法中，```DELETE```方式将会映射到控制器的```Delete```方法中,以此类推。其他非HTTP Method命名的方法，即使是定义的包公开方法，将无法完成自动注册，对于应用端不可见。当然，如果控制器并未定义对应HTTP Method的方法，该Method请求下将会返回 ```HTTP Status 404```。此外，控制器方法名称需要保证是与HTTP Method相同的公开方法且方法名首字母大写。

我们可以通过```ghttp.BindObjectRest```方法完成REST对象的注册。

[gitee.com/johng/gf/blob/master/geg/frame/mvc/controller/demo/object_rest.go](https://gitee.com/johng/gf/blob/master/geg/frame/mvc/controller/demo/object_rest.go)

```go
package demo

import "gitee.com/johng/gf/g/net/ghttp"

// 测试绑定对象
type ObjectRest struct {}

func init() {
    ghttp.GetServer().BindObjectRest("/object-rest", &ObjectRest{})
}

// RESTFul - GET
func (o *ObjectRest) Get(r *ghttp.Request) {
    r.Response.Write("RESTFul HTTP Method GET")
}

// RESTFul - POST
func (c *ObjectRest) Post(r *ghttp.Request) {
    r.Response.Write("RESTFul HTTP Method POST")
}

// RESTFul - DELETE
func (c *ObjectRest) Delete(r *ghttp.Request) {
    r.Response.Write("RESTFul HTTP Method DELETE")
}

// 该方法无法映射，将会无法访问到
func (c *ObjectRest) Hello(r *ghttp.Request) {
    r.Response.Write("Hello")
}
```