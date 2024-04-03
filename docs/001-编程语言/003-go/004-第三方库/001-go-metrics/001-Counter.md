## Counter
我们可以用这个来监控特定的事情发生了多少次

```go
package main

import (
	"fmt"
	"log"
	"time"

	"github.com/kataras/iris/v12"
	"github.com/rcrowley/go-metrics"
)

func main() {
	app := iris.New()

	c := metrics.NewCounter()
	metrics.GetOrRegister("requests", c)

	// 在单独的协程中打印 c  的信息
	go metrics.Log(metrics.DefaultRegistry, time.Second*3, log.Default())

	app.Get("/", func(ctx iris.Context) {
		name := ctx.URLParamDefault("name", "word")
		ctx.Text("hello-" + name + "  " + fmt.Sprintf("%d", c.Count()))
		// 每发生一次请求就给它 +1 一下
		c.Inc(1)
	})

	app.Listen("127.0.0.1:8080")
}
```

执行效果
```bash
webs % ./webs
Iris Version: 12.2.10

Now listening on: http://127.0.0.1:8080
Application started. Press CTRL+C to shut down.
2024/04/03 20:12:52 counter requests
2024/04/03 20:12:52   count:               0
2024/04/03 20:12:55 counter requests
2024/04/03 20:12:55   count:               1
2024/04/03 20:12:58 counter requests
2024/04/03 20:12:58   count:               2
```

---
