## iris 使用 slog
之前 golang 官方没有标准的日志库，搞的第三方的日志库多如牛毛；后面官方也推出了标准的日志库 slog 了，这里准备把 iris 项目里面的日志库改下。

```go
package main

import (
	"log/slog"
	"os"

	"github.com/kataras/iris/v12"
)

func main() {
	app := iris.New()
	logger := slog.New(slog.NewJSONHandler(os.Stderr, &slog.HandlerOptions{Level: slog.LevelDebug}))
	app.Logger().Install(logger)

	app.Get("/hello", func(ctx iris.Context) {
		ctx.Application().Logger().Info("enter hello func")

		ctx.WriteString("hello world")

		ctx.Application().Logger().Info("exit hello func")
	})

	app.Listen("127.0.0.1:8080")
}

```

---