## 概要
不少情况下我们需要让自定义的对象能序列化成 json ，并且能从 json 中还原回来。

---

## 例子
```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name string `json:"name"`
	Age  int    `json:"age"`
}

func (person *Person) MarshalJSON() ([]byte, error) {
	return json.Marshal(*person)
}

func (person *Person) UnMarshalJSON(bytes []byte) error {
	err := json.Unmarshal(bytes, person)
	return err
}

func main() {
	tom := Person{"tom", 16}
	bytes, err := tom.MarshalJSON()
	if err != nil {
		fmt.Println(err)
	}

	fmt.Println(bytes)
	fmt.Println(string(bytes))

	t := Person{}
	err = t.UnMarshalJSON(bytes)
	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("t.Name = %s, t.Age = %d \n", t.Name, t.Age)

}
```

运行效果

```bash
go run main.go 
[123 34 110 97 109 101 34 58 34 116 111 109 34 44 34 97 103 101 34 58 49 54 125]
{"name":"tom","age":16}
t.Name = tom, t.Age = 16 
```

---



