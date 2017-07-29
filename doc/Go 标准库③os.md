**Go 标准库介绍③os**

[TOC]

os 包提供了不依赖平台的操作系统函数接口。错误处理设计为go 风格，失败的调用会返回错误值而非错误码。通常错误值里包含更多信息。

# os 常用导出函数

* func Hostname() (name string, err error) // Hostname返回内核提供的主机名
* func Environ() []string // Environ返回表示环境变量的格式为”key=value”的字符串的切片拷贝
* func Getenv(key string) string // Getenv检索并返回名为key的环境变量的值
* func Getpid() int // Getpid返回调用者所在进程的进程ID
* func Exit(code int) // Exit让当前程序以给出的状态码code退出。一般来说，状态码0表示成功，非0表示出错。程序会立刻终止，defer的函数不会被执行
* func Stat(name string) (fi FileInfo, err error) // 获取文件信息
* func Getwd() (dir string, err error) // Getwd返回一个对应当前工作目录的根路径
* func Mkdir(name string, perm FileMode) error // 使用指定的权限和名称创建一个目录
* func MkdirAll(path string, perm FileMode) error // 使用指定的权限和名称创建一个目录，包括任何必要的上级目录，并返回nil，否则返回错误
* func Remove(name string) error // 删除name指定的文件或目录
* func TempDir() string // 返回一个用于保管临时文件的默认目录
* var Args []string Args保管了命令行参数，第一个是程序名。

```go
// go 标准库 os
package main
 
import (
    "fmt"
    "os"
)
 
func main() {
    // 预定义变量, 保存命令行参数
    fmt.Println(os.Args)
 
    // 获取host name
    fmt.Println(os.Hostname())
    fmt.Println(os.Getpid())
 
    // 获取全部环境变量
    env := os.Environ()
    for k, v := range env {
        fmt.Println(k, v)
    }
 
    // 终止程序
    // os.Exit(1)
 
    // 获取一条环境变量
    fmt.Println(os.Getenv("PATH"))
 
    // 获取当前目录
    dir, err := os.Getwd()
    fmt.Println(dir, err)
 
    // 创建目录
    err = os.Mkdir(dir+"/new_file", 0755)
    fmt.Println(err)
 
    // 创建目录
    err = os.MkdirAll(dir+"/new", 0755)
    fmt.Println(err)
 
    // 删除目录
    err = os.Remove(dir + "/new_file")
    err = os.Remove(dir + "/new")
    fmt.Println(err)
 
    // 创建临时目录
    tmp_dir := os.TempDir()
    fmt.Println(tmp_dir)
}
```

# File 结构体

* func Create(name string) (file *File, err error) // Create采用模式0666（任何人都可读写，不可执行）创建一个名为name的文件，如果文件已存在会截断它（为空文件）
* func Open(name string) (file *File, err error) // Open打开一个文件用于读取。如果操作成功，返回的文件对象的方法可用于读取数据；对应的文件描述符具有O_RDONLY模式
* func (f *File) Stat() (fi FileInfo, err error) // Stat返回描述文件f的FileInfo类型值
* func (f *File) Readdir(n int) (fi []FileInfo, err error) // Readdir读取目录f的内容，返回一个有n个成员的[]FileInfo，这些FileInfo是被Lstat返回的，采用目录顺序
* func (f *File) Read(b []byte) (n int, err error) // Read方法从f中读取最多len(b)字节数据并写入b
* func (f *File) WriteString(s string) (ret int, err error) // 向文件中写入字符串
* func (f *File) Sync() (err error) // Sync递交文件的当前内容进行稳定的存储。一般来说，这表示将文件系统的最近写入的数据在内存中的拷贝刷新到硬盘中稳定保存
* func (f *File) Close() error // Close关闭文件f，使文件不能用于读写

```go
// go 标准库 os.File
package main
 
import (
    "fmt"
    "os"
    "time"
)
 
func main() {
    // 获取当前目录
    dir, err := os.Getwd()
    fmt.Println(dir, err)
 
    file := dir + "/new"
    var fh *os.File
 
    fi, _ := os.Stat(file)
    if fi == nil {
        fh, _ = os.Create(file) // 文件不存在就创建
    } else {
        fh, _ = os.OpenFile(file, os.O_RDWR, 0666) // 文件存在就打开
    }
 
    w := []byte("hello go language" + time.Now().String())
    n, err := fh.Write(w)
    fmt.Println(n, err)
 
    // 设置下次读写位置
    ret, err := fh.Seek(0, 0)
    fmt.Println("当前文件指针位置", ret, err)
 
    b := make([]byte, 128)
    n, err = fh.Read(b)
    fmt.Println(n, err, string(b))
 
    fh.Close()
}
```

# FileInfo 结构体

FileInfo用来描述一个文件对象

结构体定义:

```go
type FileInfo interface {
    Name() string       // base name of the file
    Size() int64        // length in bytes for regular files; system-dependent for others
    Mode() FileMode     // file mode bits
    ModTime() time.Time // modification time
    IsDir() bool        // abbreviation for Mode().IsDir()
    Sys() interface{}   // underlying data source (can return nil)
}
```
* func Stat(name string) (fi FileInfo, err error) // Stat 返回描述文件的FileInfo。如果指定的文件对象是一个符号链接，返回的FileInfo描述该符号链接指向的文件的信息，本函数会尝试跳转该链接
* func Lstat(name string) (fi FileInfo, err error) // Lstat 返回描述文件对象的FileInfo。如果指定的文件对象是一个符号链接，返回的FileInfo描述该符号链接的信息，本函数不会试图跳转该链接。

```go
fi, _ := os.Stat(file)
if fi == nil {
    fh, _ = os.Create(file) // 文件不存在就创建
} else {
    fh, _ = os.OpenFile(file, os.O_RDWR, 0666) // 文件存在就打开
}
```


