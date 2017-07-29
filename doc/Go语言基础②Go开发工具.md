**Go语言基础②Go开发工具**

[TOC]

在Go语言中，我们很多操作都是通过go命令进行的，比如我们要执行go文件的编译，就需要使用go build命令，除了build命令之外，还有很多常用的命令，这一次我们就统一进行介绍，对常用命令有一个了解，这样我们就可以更容易的开发我们的Go程序了。

# go命令概览

打开 Mac 终端，运行 go 命令。

```go
➜  ~ go
Go is a tool for managing Go source code.

Usage:

	go command [arguments]

The commands are:

	build       compile packages and dependencies
	clean       remove object files
	doc         show documentation for package or symbol
	env         print Go environment information
	bug         start a bug report
	fix         run go tool fix on packages
	fmt         run gofmt on package sources
	generate    generate Go files by processing source
	get         download and install packages and dependencies
	install     compile and install packages and dependencies
	list        list packages
	run         compile and run Go program
	test        test packages
	tool        run specified go tool
	version     print Go version
	vet         run go tool vet on packages

Use "go help [command]" for more information about a command.

Additional help topics:

	c           calling between Go and C
	buildmode   description of build modes
	filetype    file types
	gopath      GOPATH environment variable
	environment environment variables
	importpath  import path syntax
	packages    description of package lists
	testflag    description of testing flags
	testfunc    description of testing functions

Use "go help [topic]" for more information about that topic.
```

# go build

**go build**,是非常常用的命令，它可以启动编译，把我们的包和相关的依赖编译成一个可执行的文件。

```
usage: go build [-o output] [-i] [build flags] [packages]
```

`go build`的使用比较简洁，所有的参数都可以忽略，直到只有go build，这个时候意味着使用当前目录进行编译，下面的几条命令是等价的：

```go
go build
go build .
go build hello.go
```

以上这三种写法，都是使用当前目录编译的意思。因为我们忽略了packages,所以自然就使用当前目录进行编译了。从这里我们也可以推测出，go build本质上需要的是一个路径，让编译器可以找到哪些需要编译的go文件。packages其实是一个相对路径，是相对于我们定义的GOROOT和GOPATH这两个环境变量的，所以有了packages这个参数后，go build就可以知道哪些需要编译的go文件了。

```
go build github/tools
```
这种方式是指定包的方式，这样会明确的编译我们这个包。当然我们也可以使用通配符。

```
go build github.com/tools/...
```
3个点表示匹配所有字符串，这样go build就会编译tools目录下的所有包。

# go clean

在我们使用`go build`编译的时候，会产生编译生成的文件，尤其是在我们签入代码的时候，并不想把我们生成的文件也签入到我们的Git代码库中，这时候我们可以手动删除生成的文件，但是有时候会忘记，也很麻烦，不小心还是会提交到Git中。要解决这个问题，我们可以使用`go clean`,它可以清理我们编译生成的文件，比如生成的可执行文件，生成obj对象等等。

```
usage: go clean [-i] [-r] [-n] [-x] [build flags] [packages]
```

# go run

go build是先编译，然后我们在执行可以执行文件来运行我们的程序，需要两步。go run这个命令就是可以把这两步合成一步的命令，通过go run命令，我们可以直接看到输出的结果。

```
➜  ~ go help run
usage: go run [build flags] [-exec xprog] gofiles... [arguments...]

Run compiles and runs the main package comprising the named Go source files.
A Go source file is defined to be a file ending in a literal ".go" suffix.

By default, 'go run' runs the compiled binary directly: 'a.out arguments...'.
If the -exec flag is given, 'go run' invokes the binary using xprog:
	'xprog a.out arguments...'.
If the -exec flag is not given, GOOS or GOARCH is different from the system
default, and a program named go_$GOOS_$GOARCH_exec can be found
on the current search path, 'go run' invokes the binary using that program,
for example 'go_nacl_386_exec a.out arguments...'. This allows execution of
cross-compiled programs when a simulator or other execution method is
available.
```

go run 命令需要一个go文件作为参数，这个go文件必须包含main包和main函数，这样才可以运行，其他的参数和go build差不多。在运行go run的时候，如果需要的话，我们可以给我们的程序传递参数，比如：

```
package main
import (
	"fmt"
	"os"
)
func main() {
	fmt.Println("输入的参数为：",os.Args[1])
}

打开终端，输入如下命令执行：
go run main.go 12

打印：
输入的参数为： 12
```

# go test

该命令用于Go的单元测试，它也是接受一个包名作为参数，如果没有指定，使用当前目录。
go test运行的单元测试必须符合go的测试要求。

1. 写有单元测试的文件名，必须以_test.go结尾。
2. 测试文件要包含若干个测试函数。
3. 这些测试函数要以Test为前缀，还要接收一个*testing.T类型的参数。

```go
//testing
package main

func Add(x int ,y int) int {

	return x + y
	
}
```

```go
package main
import "testing"

func TestAdd(t *testing.T) {
	if Add(1,2) == 3 {
		t.Log("1+2=3")
	}
	if Add(1,1) == 3 {
		t.Error("1+1=3")
	}
}
```
这是一个单元测试，保存在`main_test.go`文件中，对main包里的`Add(a,b int)`函数进行单元测试。
如果要运行这个单元测试，在该文件目录下，执行go test 即可。

```
➜  main go test
PASS
ok      _/Users/liuguoquan/GoglandProjects/main 0.006s

```

# go install

它和go build类似，不过它可以在编译后，把生成的可执行文件或者库安装到对应的目录下，以供使用。

```
➜  hello go help install
usage: go install [build flags] [packages]
Install compiles and installs the packages named by the import paths,
along with their dependencies.
```

它的用法和go build差不多，如果不指定一个包名，就使用当前目录。安装的目录都是约定好的，如果生成的是可执行文件，那么安装在$GOPATH/bin目录下；如果是可引用的库，那么安装在$GOPATH/pkg目录下。

# go get

**go get**命令，可以从网上下载更新指定的包以及依赖的包，并对它们进行编译和安装。

```
go get github.com/astaxie/beego
```

以上示例，我们就可以从github上直接下载这个go库到我们GOPATH工作空间中，以供我们使用。下载的是整个源代码工程，并且会根据它们编译和安装，和执行go install类似。

**go get**支持大多数版本控制系统(VCS)，比如我们常用的git，通过它和包依赖管理结合，我们可以在代码中直接导入网络上的包以供我们使用。

如果我们需要更新网络上的一个go工程，加`-u` 标记即可。

```
go get -u github.com/astaxie/beego
```
类似的，启用-v标记，可以看到下载的进度以及更多的调试信息。

# go fmt

`go fmt`可以格式化我们的源代码的布局和Go源代码一样的风格，也就是统一代码风格，这样我们再也不用为大括号要不要放到行尾还是另起一行，缩进是使用空格还是tab而争论不休了，都给我们统一了。

```go
func main() { 
	fmt.Println("输入的参数为：", os.Args[1]) }
```

比如以上代码，我们执行`go fmt` 格式化后，会变成如下这样：

```go
func main() {
	fmt.Println("输入的参数为：", os.Args[1])
}
```
go fmt也是接受一个包名作为参数，如果不传递，则使用当前目录。go fmt会自动格式化代码文件并保存，它本质上其实是调用的gofmt -l -w这个命令，我们看下gofmt的使用帮助。

```
➜  hello gofmt -h  
usage: gofmt [flags] [path ...]
  -cpuprofile string
    	write cpu profile to this file
  -d	display diffs instead of rewriting files
  -e	report all errors (not just the first 10 on different lines)
  -l	list files whose formatting differs from gofmt's
  -r string
    	rewrite rule (e.g., 'a[b:len(a)] -> a[b:]')
  -s	simplify code
  -w	write result to (source) file instead of stdout
```

# go vet

`go vet`它会帮助我们检查我们代码中常见的错误。

1. Printf这类的函数调用时，类型匹配了错误的参数。
2. 定义常用的方法时，方法签名错误。
3. 错误的结构标签。
4. 没有指定字段名的结构字面量。

```go
package main
import (
	"fmt"
)
func main() {
	fmt.Printf(" 哈哈",3.14)
}
```
这个例子是一个明显错误的例子，新手经常会犯，这里我们忘记输入了格式化的指令符，这种编辑器是检查不出来的，但是如果我们使用go vet就可以帮我们检查出这类常见的小错误。

```
➜  hello go vet
main.go:8: no formatting directive in Printf call
```

# go env

`go env` 用于查看编译环境

```
➜  ~ go env
GOARCH="amd64" //目标处理器架构
GOBIN=""
GOEXE=""
GOHOSTARCH="amd64"
GOHOSTOS="darwin"
GOOS="darwin" //目标操作系统
GOPATH="/Users/liuguoquan/go"
GORACE=""
GOROOT="/usr/local/go" //
GOTOOLDIR="/usr/local/go/pkg/tool/darwin_amd64"
GCCGO="gccgo"
CC="clang"
GOGCCFLAGS="-fPIC -m64 -pthread -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fdebug-prefix-map=/var/folders/ph/tdtx7f3167z3fzl3nqj3ht4w0000gn/T/go-build886960492=/tmp/go-build -gno-record-gcc-switches -fno-common"
CXX="clang++"
CGO_ENABLED="1"
PKG_CONFIG="pkg-config"
CGO_CFLAGS="-g -O2"
CGO_CPPFLAGS=""
CGO_CXXFLAGS="-g -O2"
CGO_FFLAGS="-g -O2"
CGO_LDFLAGS="-g -O2"
```

GOOS指的是目标操作系统，它的可用值为：

* darwin
* freebsd
* linux
* windows
* android
* dragonfly
* netbsd
* openbsd
* plan9
* solaris

GOARCH指的是目标处理器的架构，目前支持的有：

* arm
* arm64
* 386
* amd64
* ppc64
* ppc64le
* mips64
* mips64le
* s390x

GOOS和GOARCH组合起来，支持生成的可执行程序种类很多，具体组合参考[https://golang.org/doc/install/source#environment](https://golang.org/doc/install/source#environment)。如果我们要生成不同平台架构的可执行程序，只要改变这两个环境变量就可以了，比如要生成linux 64位的程序，命令如下：

```
GOOS=linux GOARCH=amd64 go build github.com/hello
```

# go help 

`go help` 用于查看命令的具体作用，比如

```
go help build
go help test
```

# Go文档

在Go语言中，Go为我们提供了快速生成文档以及查看文档的工具，让我们可以很容易的编写查看文档。

Go提供了两种查看文档的方式，一种是使用go doc命令在终端查看，这种适用于使用VIM等工具在终端开发的人员，它们不用离开终端，既可以查看想查看的文档，又可以编码。

第二种方式，是使用浏览器查看的方式，通过godoc命令可以在本机启动一个web服务，我们可以通过打开浏览器，访问这个服务来查看我们的Go文档。

## 从终端查看文档

这种方式适用于在终端开发的，它们一般不像离开终端，查完即可继续编码，这时候使用go doc命令是很不错的选择。

## 在线浏览文档

go doc终端查看的方式，虽然也很便捷，不过效率不高，并且没有查看细节以及进行跳转，为此Go为我们提供了基于浏览器使用的网页方式进行浏览API 文档，我们只用点点鼠标，就可以查看了，还可以在方法、包等之间进行跳转，更简洁方便。

要想启动一个Web在线API文档服务很简单，使用**godoc**就可以了。

```
godoc -http=:6060
```

后面的http是要指定Web服务监听的IP和Port，运行后，我们就可以打开浏览器，输入`http://127.0.0.1:6060`进行访问了，你会发现打开的页面，和GoLang的官方网站一样，没错，这个其实就是官网的一个拷贝，但是包的文档`http://127.0.0.1:6060/pkg/`会和官网不一样,你自己启动的这个服务，是基于你电脑上`GOROOT`和`GOPATH`这两个路径下的所有包生成的文档，会比官网只是标准库的文档要多。

# 参考资料

[Go开发工具](http://www.flysnow.org/2017/03/08/go-in-action-go-tools.html)
[标准命令详解](http://wiki.jikexueyuan.com/project/go-command-tutorial/0.0.html)
[Go Doc](http://www.flysnow.org/2017/03/09/go-in-action-go-doc.html)
[go doc与godoc](http://wiki.jikexueyuan.com/project/go-command-tutorial/0.5.html)

《Go语言实战》

