## Gauge

Gauge 比较适合的场景就用来记录一下当前值是多少，比如当前 MySQL 的连接数，当前 go 程序的协程数 ...

```go
package main

import (
	"fmt"
	"log"
	"runtime"
	"time"

	"github.com/kataras/iris/v12"
	"github.com/rcrowley/go-metrics"
)

func main() {
	app := iris.New()

	g := metrics.NewGauge()
	metrics.GetOrRegister("goroutines.now", g)
	go metrics.Log(metrics.DefaultRegistry, time.Second*5, log.Default())

	app.Get("/", func(ctx iris.Context) {
		ctx.Text("goroutines.now " + fmt.Sprintf("%d", g.Value()))
	})

	go func() {
		t := time.NewTicker(time.Second) //定时器
		for range t.C {
			c := runtime.NumGoroutine()
			g.Update(int64(c))
		}
	}()

	app.Listen("127.0.0.1:8080")
}
```
运行效果
```bash
./webs
Iris Version: 12.2.10

Now listening on: http://127.0.0.1:8080
Application started. Press CTRL+C to shut down.
2024/04/03 20:28:23 gauge goroutines.now
2024/04/03 20:28:23   value:               8
2024/04/03 20:28:28 gauge goroutines.now
2024/04/03 20:28:28   value:               8
```

---