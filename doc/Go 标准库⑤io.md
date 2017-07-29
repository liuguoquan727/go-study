**Go 标准库介绍⑤io**

[TOC]

# io 包的接口

* Reader

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}
```

Reader 接口用于包装基本的读取方法。Read 方法读取 len(p) 字节数据写入p。它返回写入的字节数和遇到的任何错误。即使Read方法返回值 n < len(p)，本方法在被调用时仍可能使用p的全部长度作为暂存空间。如果有部分可用数据，但不够len(p)字节，Read会返回可以读取到的数据，而不是等待更多数据。

* Writer

```go
type Writer interface {
    Write(p []byte) (n int, err error)
}
```

Writer 接口用于包装基本的写入方法。Write 方法将 len(p) 字节数据从p写入底层的数据流。它会返回写入的字节数据`(0 <= n <= len(p))` 和遇到的任何导致写入提取结束的错误。如果它返回的 n < len(p), Write必须返回非nil的错误。

* Closer

```go
type Closer interface {
    Close() error
}
```

Closer 接口用于包装基本的关闭方法。

* Seeker

```go
type Seeker interface {
    Seek(offset int64, whence int) (int64, error)
}
```

Seeker 接口用于包装基本的移位方法。Seek方法设定下一次读写的位置：偏移量为offset，校准点由whence确定：0表示相对于文件起始；1表示相对于当前位置；2表示相对于文件结尾。Seek方法返回新的位置以及可能遇到的错误。

* ReaderAt

```go
type ReaderAt interface {
    ReadAt(p []byte, off int64) (n int, err error)
}
```

ReaderAt 接口包装了基本的ReadAt方法。ReadAt从底层输入流的偏移量off位置读取len(p) 字节数据写入p， 它返回读取的字节数(0 <= n <= len(p)) 和遇到的任何错误。

* ByteReader

```go
type ByteReader interface {
    ReadByte() (c byte, err error)
}
```

ByteReader 是基本的ReadByte方法的包装。ReadByte读取输入中的单个字节并返回。如果没有字节可读取，会返回错误。

* ByteScanner

```go
type ByteScanner interface {
    ByteReader
    UnreadByte() error
}
```

ByteScanner 接口在基本的ReadByte 方法之外还添加了UnreadByte方法。UnreadByte方法让下一次调用ReadByte时返回之前调用ReadByte时返回的同一个字节。连续调用两次UnreadByte方法而中间没有调用ReadByte时，可能会导致错误

* ReaderFrom

```go
type ReaderFrom interface {
    ReadFrom(r Reader) (n int64, err error)
}
```

ReaderFrom 接口包装了基本的ReadFrom方法。ReadFrom 方法从r 读取数据直到EOF或者遇到错误。返回值n是读取的字节数，执行时遇到的错误（EOF除外）也会被返回

* LimitedReader

```go
type LimitedReader struct {
    R Reader // underlying reader
    N int64  // max bytes remaining
}
```

LimitedReader 从R中读取数据，但限制可以读取的数据的量为最多N 字节，每次调用Read方法都会更新N 以标记剩余可以读取的字节数。

* func LimitReader(r Reader, n int64) LimitedReader // 创建一个 LimitedReader
* func (l *LimitedReader) Read(p []byte) (n int, err error)

* SectionReader

```go
type SectionReader struct {
    r     ReaderAt
    base  int64
    off   int64
    limit int64
}
```

SectionReader 实现了对底层满足ReadAt 接口的输入流某个片段的Read、ReadAt、Seek方法。

* func NewSectionReader(r ReaderAt, off int64, n int64) *SectionReader // 返回一个从r中的偏移量off处为起始，读取n个字节后以停止的SectionReader
* func (s *SectionReader) Size() int64 // Size返回该片段的字节数

# io 导出函数

* func WriteString(w Writer, s string) (n int, err error) // 将字符串s的内容写入w中
* func Copy(dst Writer, src Reader) (written int64, err error) // 将src的数据拷贝到dst，直到在src上到达EOF或发生错误。返回拷贝的字节数和遇到的第一个错误

```go
package main
 
import (
    "fmt"
    "io"
    "log"
    "os"
    "strings"
)
 
func main() {
    r := strings.NewReader("go is a programming language\n")
    if n, err := io.Copy(os.Stdout, r); err != nil {
        log.Fatal(n, err)
    }
 
    // writestring
    io.WriteString(os.Stdout, "hello go\n")
 
    // 从标准输入copy 到标准输出
    if n, err := io.Copy(os.Stdout, os.Stdin); err != nil {
        fmt.Println(n, err)
    }
}
```


