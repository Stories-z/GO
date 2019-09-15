# Go环境的安装
下载二进制包：go1.13.linux-amd64.tar.gz。
将下载的二进制包解压至 /usr/local目录：
```tar -C /usr/local -xzf go1.13.linux-amd64.tar.gz ```。
将 /usr/local/go/bin 目录添加至PATH环境变量：
```export PATH=$PATH:/usr/local/go/bin```。
# 配置GOPATH 环境变量
配置工作空间：
```mkdir $HOME/work```
```export GOPATH=$HOME/work```。
将此工作空间的 bin 子目录添加到 PATH 中：
```export PATH=$PATH:$GOPATH/bin```。
# 编写你的第一个程序
要编译并运行简单的程序，首先要选择包路径（我们在这里使用 github.com/user/hello），并在工作空间内创建相应的包目录：
```mkdir $GOPATH/src/github.com/user/hello``` 。

接着，在该目录中创建名为 hello.go 的文件，其内容为以下Go代码：
```
package main

import "fmt"

func main() {
	fmt.Printf("Hello, world.\n")
}
```
现在使用 go 工具构建并安装此程序了：
```go install github.com/user/hello```。
因为已经将 $GOPATH/bin 添加到 PATH 中了，只需输入该二进制文件名即可：
```hello```。
输出结果为：
$Hello, world.

# 配置第一个库
让我们编写一个库，并让 hello 程序来使用它。
同样，第一步还是选择包路径（我们将使用 github.com/user/stringutil） 并创建包目录：
```mkdir $GOPATH/src/github.com/user/stringutil```。
接着，在该目录中创建名为 reverse.go 的文件，内容如下：
```
package stringutil
func Reverse(s string) string {
	r := []rune(s)
	for i, j := 0, len(r)-1; i < len(r)/2; i, j = i+1, j-1 {
		r[i], r[j] = r[j], r[i]
	}
	return string(r)
}
```
现在用 go build 命令来测试该包的编译：
``` go build github.com/user/stringutil``` 。
当然，若在该包的源码目录中，只需执行：
``` go build``` 
即可。这不会产生输出文件。想要输出的话，必须使用 go install 命令，它会将包的对象放到工作空间的 pkg 目录中。
确认 stringutil 包构建完毕后，修改原来的 hello.go 文件（它位于$GOPATH/src/github.com/user/hello）去使用它：
```
package main

import (
	"fmt"

	"github.com/user/stringutil"
)

func main() {
	fmt.Printf(stringutil.Reverse("\n!oG ,olleH"))
}
```
无论是安装包还是二进制文件，go 工具都会安装它所依赖的任何东西。 因此当我们通过
```go install github.com/user/hello```
来安装 hello 程序时，stringutil 包也会被自动安装。
运行此程序的新版本：
``` hello``` 
能看到一条新的，反向的信息：
$Hello, Go!

# 测试
Go拥有一个轻量级的测试框架，它由 go test 命令和 testing 包构成。
我们可通过创建文件 $GOPATH/src/github.com/user/stringutil/reverse_test.go 来为 stringutil 添加测试，其内容如下：
```
package stringutil

import "testing"

func TestReverse(t *testing.T) {
	cases := []struct {
		in, want string
	}{
		{"Hello, world", "dlrow ,olleH"},
		{"Hello, 世界", "界世 ,olleH"},
		{"", ""},
	}
	for _, c := range cases {
		got := Reverse(c.in)
		if got != c.want {
			t.Errorf("Reverse(%q) == %q, want %q", c.in, got, c.want)
		}
	}
}
```
接着使用 go test 运行该测试：
``` go test github.com/user/stringutil``` 。
输出结果为：
$ok  	github.com/user/stringutil 0.165s。
同样，若在包目录下运行 go 工具，也可以忽略包路径：
```go test```。
输出结果为：
$ok  	github.com/user/stringutil 0.165s



