**Go 标准库介绍①string**

[TOC]

strings 包实现了用于操作字符串的简单函数，包括 string 导出函数和 Reader, Replacer 两个结构体。

# strings 常用导出函数

## 判断字符串与子串关系

* func EqualFold(s, t string) bool // 判断两个utf-8编码字符串，大小写不敏感
* func HasPrefix(s, prefix string) bool // 判断s是否有前缀字符串prefix
* func Contains(s, substr string) bool // 判断字符串s是否包含子串substr
* func ContainsAny(s, chars string) bool // 判断字符串s是否包含字符串chars中的任一字符
* func Count(s, sep string) int // 返回字符串s中有几个不重复的sep子串
* 获取字符串中子串位置

* func Index(s, sep string) int // 子串sep在字符串s中第一次出现的位置，不存在则返回-1
* func IndexByte(s string, c byte) int // 字符c在s中第一次出现的位置，不存在则返回-
* func IndexAny(s, chars string) int // 字符串chars中的任一utf-8码值在s中第一次出现的位置，如果不存在或者chars为空字符串则返回-1
* func IndexFunc(s string, f func(rune) bool) int // s中第一个满足函数f的位置i（该处的utf-8码值r满足f(r)==true），不存在则返回-1
* func LastIndex(s, sep string) int // 子串sep在字符串s中最后一次出现的位置，不存在则返回-1

## 字符处理

* func Title(s string) string // 返回s中每个单词的首字母都改为标题格式的字符串拷贝
* func ToLower(s string) string // 返回将所有字母都转为对应的小写版本的拷贝
* func ToUpper(s string) string // 返回将所有字母都转为对应的大写版本的拷贝
* func Repeat(s string, count int) string // 返回count个s串联的字符串
* func Replace(s, old, new string, n int) string // 返回将s中前n个不重叠old子串都替换为new的新字符串，如果n<0会替换所有old子串
* func Map(mapping func(rune) rune, s string) string // 将s的每一个unicode码值r都替换为mapping(r)，返回这些新码值组成的字符串拷贝。如果mapping返回一个负值，将会丢弃该码值而不会被替换

## 字符串前后端处理

* func Trim(s string, cutset string) string // 返回将s前后端所有cutset包含的utf-8码值都去掉的字符串
* func TrimSpace(s string) string // 返回将s前后端所有空白（unicode.IsSpace指定）都去掉的字符串
* func TrimFunc(s string, f func(rune) bool) string // 返回将s前后端所有满足f的unicode码值都去掉的字符串

## 分割与合并

* func Fields(s string) []string // 返回将字符串按照空白（通过unicode.IsSpace判断，可以是一到多个连续的空白字符）分割的多个字符串
* func Split(s, sep string) []string // 用去掉s中出现的sep的方式进行分割，会分割到结尾，并返回生成的所有片段组成的切片
* func Join(a []string, sep string) string // 将一系列字符串连接为一个字符串，之间用sep来分隔

# Reader结构体

Reader 类型从一个字符串读取数据，实现了io.Reader, io.Seeker等接口。

* func NewReader(s string) *Reader // 通过字符串 s 创建一个 Reader
* func (r *Reader) Len() int // 返回 r 还没有读取部分的长度
* func (r *Reader) Read(b []byte) (n int, err error) // 读取部分数据到 b 中，读取的长度取决于 b 的容量
* func (r *Reader) ReadByte() (b byte, err error) // 从 r 中读取一字节数据

```go
// go 标准库 strings.Reader
package main
 
import (
    "fmt"
    "strings"
)
 
func main() {
    s := "hello world"
    // 创建 Reader
    r := strings.NewReader(s)
 
    fmt.Println(r) // &amp;{hello world 0 -1}
    fmt.Println(r.Size()) // 11 获取字符串长度
    fmt.Println(r.Len()) // 11 获取未读取长度
 
    // 读取前6个字符
    for r.Len() < 5 {
        b, err := r.ReadByte() // 读取1 byte
        fmt.Println(string(b), err, r.Len(), r.Size())
        // h &lt;nil&gt; 10 11
        // e &lt;nil&gt; 9 11
        // l &lt;nil&gt; 8 11
        // l &lt;nil&gt; 7 11
        // o &lt;nil&gt; 6 11
        //   &lt;nil&gt; 5 11
    }
 
    // 读取还未被读取字符串中5字符的数据
    b_s := make([]byte, 5)
    n, err := r.Read(b_s)
    fmt.Println(string(b_s), n ,err) // world 5 &lt;nil&gt;
    fmt.Println(r.Size()) // 11
    fmt.Println(r.Len()) // 0
}
```

#  Replacer 结构体

Replacer 类型实现字符串替换的操作

* func NewReplacer(oldnew ...string) *Replacer // 使用提供的多组old、new字符串对创建一个*Replacer
* func (r *Replacer) Replace(s string) string // 返回s 所有替换完后的拷贝
* func (r *Replacer) WriteString(w io.Writer, s string) (n int, err error) // 向w中写入s替换完后的拷贝

```go
// go 标准库 strings.Replacer
package main
 
import (
    "fmt"
    "strings"
    "os"
)
 
func main() {
    s := "
 
Go Language
 
"
    r := strings.NewReplacer("&lt;", "&lt;", "&gt;", "&gt;")
    fmt.Println(r.Replace(s))
 
    r.WriteString(os.Stdout, s)
}
```

