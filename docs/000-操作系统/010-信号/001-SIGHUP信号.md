# SIGHUP 信号
当终端检测到一个连接断开，则发送 SIGHUP 信号到终端相关会话的**首进程**；当首进程退出的时候，也会发送 SIGHUP 到进程组里面的其它进程。这样整个终端相关的进程就都退出了，这就是对于需要长时间执行的任务我们需要放后台执行的原因了。
```bash 
nohup /bin/xxx >/dev/null 2>/dev/null &
```
根据 SIGHUP 这个信号名，我们不难想到放后台执行的命令会叫 nohup 的原因了吧。

---

## 守护进程
由于守护进程不隶属任何一个终端，理论上它永久不会收到 SIGHUP 信号；传统上来看 SIGHUP 信号通常用作一个哨兵，当程序员给守护进程发送 SIGHUP 信号时，就执行一段特殊逻辑，通常这段逻辑是读取配置文件。

---

## golang-示例
```go
package main

import (
	"log/slog"
	"os"
	"os/signal"
	"syscall"
	"time"
)

func acceptSignals() {
	c := make(chan os.Signal, 1)

	signal.Notify(c, syscall.SIGHUP)
	signal.Notify(c, syscall.SIGTERM)
	go func() {
		for sig := range c {
			switch sig {
			case syscall.SIGHUP:
				slog.Info("收到 SIGHUP 信号")
			case syscall.SIGTERM:
				slog.Info("收到 SIGTERM 信号")
				os.Exit(0)
			}
		}
	}()
	slog.Info("信号注册完成")
}

func main() {
	slog.Info("主协程启动 .")
	acceptSignals()

	// sleep 5 分钟
	slog.Info("主协程进入 sleep .")
	time.Sleep(5 * 60 * time.Second)
	slog.Info("主协程退出 .")
}

```
编译并运行程序, 通过 ps 看到进程的 pid 是 136940, 现在我们通过 kill 向程序发送 SIGHUP 信号
```
kill -s SIGHUP 136940
kill -s SIGHUP 136940
kill -s SIGHUP 136940
```
---

程序的日志如下
```
2023/10/05 23:46:12 INFO 主协程启动 .
2023/10/05 23:46:12 INFO 信号注册完成
2023/10/05 23:46:12 INFO 主协程进入 sleep .
2023/10/05 23:46:33 INFO 收到 SIGHUP 信号
2023/10/05 23:46:34 INFO 收到 SIGHUP 信号
2023/10/05 23:46:35 INFO 收到 SIGHUP 信号
```

---

## python-示例
```python

```

---
