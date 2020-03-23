
# 滚动切分

之前的章节中我们知道，`glog`模块支持通过设置日志文件名称的方式，使得日志文件按照日期进行输出。此外，`glog`模块也支持对日志文件进行滚动切分的特性，该特性涉及到日志对象配置属性中的几个配置项：
```go
RotateSize     int64          // Enables the rotate feature by set the size > 0 in bytes.
RotateBackups  int            // Max backups for rotated files, default is 0, means no backups.
RotateExpire   time.Duration  // Max expire age for rotated files. It's 0 in default, means no expiration.
RotateCompress int            // Compress level for rotated files using gzip algorithm. It's 0 in default, means no compression.
RotateInterval time.Duration  // Asynchronizely checks the backups and expiration at intervals. It's 1 minute in default.
```
简要说明如下：
1. `RotateSize`用于设置滚动切分时按照文件大小进行切分，该属性的单位为字节。只有当该属性值大于0时才会开启滚动切分的特性。
1. `RotateBackups`用于设置滚动切分的保留文件数，默认为0表示不保留，往往该值需要设置大于0。超过该保留文件数的切分文件将会按照从旧到新进行删除。
1. `RotateExpire`用于设置按照过期时间进行清理。当切分文件超过指定的时间时将会被删除。
1. `RotateCompress`用于设置切分文件的压缩级别，默认为0表示不压缩。该压缩级别的取值范围为`1-9`，其中`9`为最大压缩级别。
1. `RotateInterval`用于设置定时器的定时检测间隔，默认为1分钟，往往不需要设置。

滚动切分的配置项也支持在`logger`的配置文件中进行配置，使用非常方便。













