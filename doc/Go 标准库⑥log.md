**Go 标准库介绍⑥log**

[TOC]

>log 包实现了简单的日志服务，log 包定义了Logger类型，该类型提供了一些格式化输出的方法。本包也提供了一个预定义的“标准”Logger，可以通过辅助函数Print[f|ln]、Fatal[f|ln]和Panic[f|ln]访问，比手工创建一个Logger对象更容易使用。Logger会打印每条日志信息的日期、时间，默认输出到标准错误。Fatal系列函数会在写入日志信息后调用os.Exit(1)。Panic系列函数会在写入日志信息后调用panic。

# Logger结构体

```go
type Logger struct {
    mu     sync.Mutex // ensures atomic writes; protects the following fields
    prefix string     // prefix to write at beginning of each line
    flag   int        // properties
    out    io.Writer  // destination for output
    buf    []byte     // for accumulating text to write
}
```

Logger 类型表示一个记录日志的对象，它会生成一行行的输出写入一个io.Writer 接口。每一条日志操作会调用一次io.Writer 接口的Write 方法。Logger 类型的对象可以被多个线程安全的同时使用，它会保证对io.Writer 接口的顺序访问。

* func New(out io.Writer, prefix string, flag int) *Logger // 创建一个Logger, out设置日志信息写入的目的地, prefix 添加到生成的每一条日志前面, flag定义日志的属性

* func (l *Logger) Flags() int // Flags返回logger的输出选项

* func (l *Logger) SetFlags(flag int) // SetFlags设置logger的输出选项

* func (l *Logger) Prefix() string // Prefix返回logger的输出前缀

* func (l *Logger) SetPrefix(prefix string) // SSetPrefix设置logger的输出前缀

* func (l *Logger) Output(calldepth int, s string) error // 输出一条日志

* func (l *Logger) Printf(format string, v ...interface{}) // 将生成的格式化字符串输出到out

* func (l *Logger) Fatal(v ...interface{}) // Fatal等价于{l.Print(v…); os.Exit(1)}

* func (l *Logger) Panic(v ...interface{}) // Panic等价于{l.Print(v…); panic(…)}

# log导出函数

log 导出函数基于预定义的 std 变量 `var std = New(os.Stderr, "", LstdFlags)` 实现。

* func Flags() int // 获取预定义“标准”日志输出属性
* func Prefix() string // 获取预定义“标准”日志前缀
* func SetOutput(w io.Writer) // 设置预定义“标准”日志输出位置
* func Print(v ...interface{}) // 打印一条标准日志

# 示例

```go
// go 标准库 log
package main
 
import (
    "fmt"
    "log"
    "os"
)
 
func main() {
    logger := log.New(os.Stdout, "a log:", log.Lshortfile)
    logger.SetFlags(log.Llongfile)
    fmt.Println(logger.Flags()) // 8
 
    logger.SetPrefix("debug:")
    fmt.Println(logger.Prefix()) // debug:
 
    logger.Print("hello, log file")
 
    err := logger.Output(2, "world")
    fmt.Println(err)
 
    logger.Printf("%v\t%v", "hello", "go")
 
    // logger.Fatal("fatal log")
    // logger.Panic("Panic log")
 
    // 使用预定义日志类型
    fmt.Println(log.Flags())         // 3
    fmt.Println(log.Prefix())        // ''
    log.Print("hello predefine log") // 2017/03/04 16:34:27 hello predefine log
}
```

