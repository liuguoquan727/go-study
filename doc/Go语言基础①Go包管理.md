**Go语言基础①Go包管理**

[TOC]

# 简介

我们在使用其他语言，比如Java，是有包的概念的，它是Java语言中组织我们的Java文件的一个概念，比如java.lang这个包，他里面有很多我们常用的类，比如String。在Go语言中，包也是类似的概念，它是把我们的go文件组织起来，可以方便进行归类、复用等目的。 比如Go内置的net包

```go
net
├── http
├── internal
├── mail
├── rpc
├── smtp
├── testdata
├── textproto
└── url
```

以上是net包的一个目录结构，net本身是一个包，net目录下的http又是一个包。从这个大家可以看到，go语言的包其实就是我们计算机里的目录，或者叫文件夹，通过它们进行目录结构和文件组织，go只是对目录名字做了一个翻译，叫【包】而已。比如这里的net包其实就是net目录，http包其实就是http目录，这也是go语言中的一个命名习惯，包名和文件所在的目录名是一样的。

# 包的命名

go 语言包的命名，**遵循简洁、小写、和 go 文件所在目录同名的原则**，这样就便于我们引用，书写以及快速定位查找。

比如go自带的http这个包，它这个http目录下的所有go文件都属于这个http包,所以我们使用http包里的函数、接口的时候，导入这个http包就可以了。

```go
package main
import "net/http"
func main() {
	http.ListenAndServe("127.0.0.1:8080",handler);
}
```

从这个例子可以看到，我们导入的是**net/http**,在这里叫做全路径，因为 http 包在 net 里面，net 是最顶级的包，所以必须使用全路径导入，go 编译程序才能找到 http 这个包，和我们文件系统的目录路径是一样的。

因为有了全路径，**所以命名的包名可以和其他库的一样，只要它们的全路径不同就可以了**，使用全路径的导入，也增加了包名命名的灵活性。

对于自己或者公司开发的程序而言，我们一般采用域名作为顶级包名的方式，这样就不用担心和其他开发者包名重复的问题了，假设我的个人域名是 **www.deason.com**,那么我自己开发的go程序都以 **deason.com** 作为全路径中的最顶层部分，比如导入我开发的一个工具包:

```
package main
import "deason.com/tools"
```

如果你没有自己的域名，怎么办呢？这时候可以使用 **github.com**。这时候我们就可以使用 **github.com/<username>** 作为你的顶级路径了，别人是不会和你重名的。

```
package main
import "github.com/liuguoquan/tools"
```

这就是换成github.com命名的方式。

# main包

当把一个go文件的包名声明为main时，就等于告诉go编译程序，我这个是一个可执行的程序，那么go编译程序就会尝试把它编译为一个二进制的可执行文件。

一个main的包，一定会包含一个**main()**函数，比如C和Java都有main()函数,它是一个程序的入口，没这个函数，程序就无法执行。

```go
package main
import "fmt"
func main() {
	fmt.Println("Hello, 世界")
}
```

假设该go文件叫hello.go,放在**$GOPATH/src/hello**目录下，那么我们在这个目录下执行go build命令就会生成二进制的可执行文件，在window系统下生成的是hello.exe，在Unix，MAC和Linux下生成的是hello,我们在CMD或者终端里执行它，就可以看到控制台打印的:

```
Hello, 世界
```

# 导入包

要想使用一个包，必须先导入它才可以使用，Go语言提供了import关键字来导入一个包，这个关键字告诉Go编译器到磁盘的哪里去找要想导入的包，所以导入的包必须是一个全路径的包，也就是包所在的位置。

```
import "fmt"
```

**如果要导入多个包怎么办呢？Go语言还为我们提供的导入块**。

```go
import (
	"net/http"
	"fmt"
)

或者

import "fmt"
import "net/http"
```

>对于多于一个路径的包名，在代码中引用的时候，使用全路径最后一个包名作为引用的包名，比如net/http,我们在代码使用的是http，而不是net。

现在我导入了包，那么编译的时候，go编译器去什么位置找他们呢？这里就要介绍下Go的环境变量了。Go有两个很重要的环境变量**GOROOT**和**GOPATH**,这是两个定义路径的环境变量，GOROOT是安装Go的路径，比如**/usr/local/go**；**GOPATH** 是我们自己定义的开发者个人的工作空间，比如**/home/liuguoquan/go**。

编译器会使用我们设置的这两个路径，再加上**import**导入的相对全路径来查找磁盘上的包，比如我们导入的fmt包，编译器最终找到的是/usr/local/go/fmt这个位置。

值得了解的是：对于**包的查找，是有优先级的**，编译器会优先在GOROOT里搜索，其次是GOPATH,一旦找到，就会马上停止搜索。如果最终都没找到，就报编译异常了。

# 远程包导入

如果有的Go包共享在Github上，我们一样有办法使用他们，这就是远程导入包了，或者是网络导入，Go天生就支持这种情况，所以我们可以很随意的使用Github上的Go库开发程序。

```
import "github.com/astaxie/beego"
```

这种导入，前提必须是该包托管在一个分布式的版本控制系统上，比如Github、Bitbucket等，并且是Public的权限，可以让我们直接访问它们。

编译在导入它们的时候，会先在**GOPATH**下搜索这个包，如果没有找到，就会使用go get工具从版本控制系统（GitHub）获取，并且会把获取到的源代码存储在GOPATH目录下对应URL的目录里，以供编译使用。

`go get`工具可以递归获取依赖包，如果`github.com/astaxie/beego`也引用了其他的远程包，该工具可以一并下载下来。

# 命名导入包

我们知道，在使用`import`关键字导入包之后，我们就可以在代码中通过包名使用该包下相应的函数、接口等。如果我们导入的包名正好有重复的怎么办呢？针对这种情况，Go语言可以让我们对导入的包重新命名，这就是命名导入。

```go
package main
import (
	"fmt"
	myfmt "mylib/fmt"
)
func main() {
	fmt.Println()
	myfmt.Println()
}
```

如果没有重新命名，那么对于编译器来说，这两个fmt它是区分不清楚的。重命名也很简单，在我们导入的时候，在包名的左侧，起一个新的包名就可以了。

**Go语言规定，导入的包必须要使用，否则会包编译错误**，这是一个非常好的规则，因为这样可以避免我们引用很多无用的代码而导致的代码臃肿和程序的庞大，因为很多时候，我们都不知道哪些包是否使用，这在C和Java上会经常遇到，有时候我们不得不借助工具来查找我们没有使用的文件、类型、方法和变量等，把它们清理掉。

但是有时候，我们需要导入一个包，但是又不使用它，按照规则，这是不行的，为此Go语言给我们提供了一个空白标志符`_`,只需要我们使用`_`重命名我们导入的包就可以了。

```
package main
import (
	_ "mylib/fmt"
)

或者

import _ "mylib/fmt"
```

# 包的init函数

每个包都可以有任意多个init函数，这些init函数都会在main函数之前执行。init函数通常用来做初始化变量、设置包或者其他需要在程序执行前的引导工作。比如上面我们讲的需要使用_空标志符来导入一个包的目的，就是想执行这个包里的init函数。

我们以数据库的驱动为例，Go语言为了统一关于数据库的访问，使用databases/sql抽象了一层数据库的操作，可以满足我们操作MYSQL、Postgre等数据库，这样不管我们使用这些数据库的哪个驱动，编码操作都是一样的，想换驱动的时候，就可以直接换掉，而不用修改具体的代码。

这些数据库驱动的实现，就是具体的，可以由任何人实现的，它的原理就是定义了init函数，在程序运行之前，把实现好的驱动注册到sql包里，这样我们就使用使用它操作数据库了。

```go
package mysql
import (
	"database/sql"
)
func init() {
	sql.Register("mysql", &MySQLDriver{})
}
```

因为我们只是想执行这个mysql包的init方法，并不想使用这个包，所以我们在导入这个包的时候，需要使用`_`重命名包名，避免编译错误。

```go
import "database/sql"
import _ "github.com/go-sql-driver/mysql"

db, err := sql.Open("mysql", "user:password@/dbname")
```

看非常简洁，剩下针对的数据库的操作，都是使用的`database/sql`标准接口，如果我们想换一个`mysql`的驱动的话，只需要换个导入就可以了，灵活方便，这也是面向接口编程的便利。

# 参考资料

[Go包管理](http://www.flysnow.org/2017/03/04/go-in-action-go-package.html)

《Go语言实战》




