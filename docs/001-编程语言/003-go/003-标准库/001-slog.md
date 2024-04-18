# slog 统一日志库
在 go-1.21.0 版本之前，对于如果打印日志这件事上一直没有形成一个官方的规范；如果官方不作为第三方就会各有各的想法了，搞的打印个日志都五花八门。 标准库 slog 出来之后总算是结束了这一乱世。

---

## logger
logger 是日志库的前端，它暴露了 Info, Debug ... 这些用于打印日志的接口；另外 slog 库为了方便我们使用它提供了一个默认的 logger 对象给顶层的方法使用。下面来试用一下这个默认的 logger 。
```go
package main

import "log/slog"

func main() {
	slog.Info("message", "key", "value")
}
n
```
效果
```
go run main.go 
2023/09/27 23:54:16 INFO message key=value
```
由于默认 logger 的存在，使得 slog 对于最小化使用的场景，基本做到了导入进来就能用。但是多了解一点的话，我就能用得更加得心就手了。

---

## handler

正如前面所说的那样，logger 只是前端，真正干活的是与 logger 关联的 handler 对象； 也就是说如果我们要做一些定制，就必须通过 handler 来搞。
```go
package main

import (
	"log/slog"
	"os"
)

func main() {
	// text logger 
	// logger := slog.New(slog.NewTextHandler(os.Stderr, nil))

	// json logger
	logger := slog.New(slog.NewJSONHandler(os.Stdout, nil))
	logger.Info("message", "key", "value")
}
```
运行效果
```
go run main.go
{"time":"2023-09-27T23:52:46.321387+08:00","level":"INFO","msg":"message","key":"value"}
```
---

## 设置全局默认的 logger 对象
前面提到的这个默认 logger 对象也是可以改的。
```go
package main

import (
	"log/slog"
	"os"
)

func main() {
	logger := slog.New(slog.NewJSONHandler(os.Stdout, nil))
	slog.SetDefault(logger)

	// 使用全局的 Info 函数
	slog.Info("message", "key", "value")
}

```
运行效果
```
{"time":"2023-09-27T23:57:22.378699+08:00","level":"INFO","msg":"message","key":"value"}
```
---
