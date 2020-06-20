switch通过判断变量的值，将执行不同的语句，格式如下：

```go
switch <变量> {
  case <value1>:
  	......
  case <value2>:
  	......
  case <value3>:
  	......
  default:
  	......
}
```



**switch会自动break**



例如：

```go
package main

import "fmt"

func grade(score int) string {
	g := ""
	switch {
	case score < 0 || score > 100:
		panic(fmt.Sprintf("wrong score: %d", score))
	case score < 60:
		g = "F"
	case score < 80:
		g = "C"
	case score <90:
		g = "B"
	case score <= 100:
		g = "A"
	}
	return g
}

func main() {
	fmt.Println(grade(0))
}

```



> Switch 后边可以没有表达式，可以在case时进行判断。