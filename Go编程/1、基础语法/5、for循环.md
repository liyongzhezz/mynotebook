for循环语句结构：

```go
for <循环条件> {
  循环体
}
```

**循环条件不需要括号，for循环可以省略初四条件，结束条件，递增表达式**



例如：

```go
package main

import (
	"fmt"
	"strconv"
)

func convertToBin(n int) string {
	result := ""
	for ; n>0; n/=2 {
		lsb := n % 2
		result = strconv.Itoa(lsb) + result
	}
	return result
}

func main() {
	fmt.Println(
		convertToBin(5),  // 输出 101
		convertToBin(13)	,	// 输出 1101
	)
}

```



如果for的条件什么都不写，就是一个死循环，例如如下的函数：

```go
func forver() {
  for {
    fmt.Println("abcd")
  }
}
```

