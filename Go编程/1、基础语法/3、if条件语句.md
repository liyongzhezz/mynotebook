条件判断语句也是使用if/else结构来实现，基本结构如下：

```go
if <条件> {
  ......
} else if <条件> {
  ......
} else {
  ......
}
```



实例：

```go
package main

import (
	"fmt"
	"io/ioutil"
)

func main() {
	const filename = "abc.txt"
	contents, err := ioutil.ReadFile(filename)
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Printf("%s\n", contents)
	}
}
```



这个代码会读取一个文件的内容，并判断`err`不为空（读取文件失败），则打印错误信息，否则打印文件内容。



上边的代码可以简写为：

```go
package main

import (
	"fmt"
	"io/ioutil"
)

func main() {
	const filename = "abc.txt"
	if contents, err := ioutil.ReadFile(filename); err != nil {
		fmt.Println(err)
	} else {
		fmt.Printf("%s\n", contents)
	}
}
```



**注意的是，contents和err是在if中定义的，所以这两个变量只在if内才能使用，在if外部无法使用**