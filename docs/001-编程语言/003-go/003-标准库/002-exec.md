## exec 库
golang 中用 exec 封装了执行命令的功能，就用法上来看可以分成如下几个场景。

---

## 简单执行
就是说只要执行一下命令，并不关注结果
```go
package main

import (
	"fmt"
	"os/exec"
)

func main() {
	cmd := exec.Command("dbm-agent", "stop")
	if err := cmd.Run(); err != nil {
		fmt.Println(err.Error())
	}
}
```

---

## 简单的获取结果
之所以说是简单，是因为这个不区分 stderr 和 stdout ，反正都给它拿到
```go
package main

import (
	"fmt"
	"os/exec"
)

func main() {
	cmd := exec.Command("date", "-I")
	stderr_and_stdin, err := cmd.CombinedOutput()
	if err != nil {
		fmt.Println(err.Error())
	} else {
		fmt.Println(string(stderr_and_stdin))
	}
}
```

---

## 执行 shell 脚本
执行简单的 shell 脚本并获取输出
```go
package main

import (
	"fmt"
	"os/exec"
)

func main() {
	cmd := exec.Command("bash", "/tmp/a.sh")
	stderr_and_stdin, err := cmd.CombinedOutput()
	if err != nil {
		fmt.Println(err.Error())
	} else {
		fmt.Println(string(stderr_and_stdin))
	}
}
```

---