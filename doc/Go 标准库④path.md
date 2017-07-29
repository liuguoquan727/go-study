**Go 标准库介绍④path**

[TOC]

# path 包

path实现了对斜杠分隔的路径进行操作的函数。

* func IsAbs(path string) bool // 判断是否是一个绝对路径
* func Split(path string) (dir, file string) // 将路径分割为路径和文件名
* func Join(elem ...string) string // 将多个字符串合并为一个路径
* func Ext(path string) string // 返回路径中扩展部分
* func Base(path string) string // 返回路径的最后一个元素
* func Dir(path string) string // 返回路径中目录部分
* func Clean(path string) string // 返回同目录的最短路径
* func Match(pattern, name string) (matched bool, err error) // 正则是否匹配路径（shell 文件名匹配）

```go
// go 标准库 path
package main
 
import (
    "fmt"
    "path"
)
 
func main() {
    pt := "~/gocode/src/go_note/package/path"
 
    // 判断是否是一个绝对路径
    is_abs := path.IsAbs(pt)
    fmt.Println(is_abs) // false
 
    // 将路径分割为路径和文件名
    pf := "~gocode/src/go_note/package/path/path.go"
    dir, file := path.Split(pf)
    fmt.Println(dir, file) // ~gocode/src/go_note/package/path/ path.go
 
    // 将多个字符串合并为一个路径
    dir_join := path.Join("usr", "local", "bin")
    fmt.Println(dir_join) // usr/local/bin
 
    // 返回路径中扩展
    file_ext := path.Ext(pf)
    fmt.Println(file_ext) // .go
 
    // 返回路径的最后一个元素
    dir_base := path.Base(pf)
    fmt.Println(dir_base) // path.go
 
    // 返回路径中目录部分
    dir = path.Dir(pf)
    fmt.Println(dir) // ~gocode/src/go_note/package/path
 
    // 返回同目录的最短路径
    dir_a := "/usr/../etc/../etc/ssh"
    fmt.Println(path.Clean(dir_a)) // /etc/ssh
 
    // 正则是否匹配路径
    is_match, err := path.Match("*.xml", "a.xml")
    fmt.Println(is_match, err) // true &lt;nil&gt;
}
```

# path/filepath 包

filepath 包实现了兼容各操作系统的文件路径操作函数。

* filepath.Separator // 预定义变量，表示路径分隔符 /
* filepath.ListSeparator // 预定义变量，表示环境变量分隔符 :
* func Abs(path string) (string, error) // 返回path 相对当前路径的绝对路径
* func Clean(path string) string // 返回path 的最短路径
* func Rel(basepath, targpath string) (string, error) // 返回targpath 相对 basepath路径
* func EvalSymlinks(path string) (string, error) // 返回软链指向的路径
* func VolumeName(path string) string // 返回路径最前面的卷名
* func ToSlash(path string) string // 路径分隔符替换为 /
* func FromSlash(path string) string // / 替换为路径分隔符
* func SplitList(path string) []string // 分隔环境变量里面的路径
* func Walk(root string, walkFn WalkFunc) error // 遍历 root 目录下的文件树，并调用 walkFn

```go
// go 标准库 path/filepath
package main
 
import (
    "fmt"
    "os"
    "path/filepath"
)
 
// 打印路径名称
func pathName(path string, info os.FileInfo, err error) error {
    if err != nil {
        return err
    } else {
        fmt.Println(path)
    }
    return nil
}
 
func main() {
    // 预定义变量
    fmt.Println(string(filepath.Separator), string(filepath.ListSeparator))
 
    // 返回path 相对当前路径的绝对路径
    dir := "~/gocode/src/go_note/package/filepath"
    real_dir, err := filepath.Abs(dir)
    fmt.Println(real_dir, err)
 
    // 返回path 的最短路径
    dir = "/usr/../etc/../tmp"
    clear_dir := filepath.Clean(dir)
    fmt.Println(clear_dir) // /tmp
 
    // 返回targpath 相对 basepath路径
    basepath, targpath := "/usr/local", "/usr/local/go/bin"
    rel_dir, err := filepath.Rel(basepath, targpath)
    fmt.Println(rel_dir, err) // go/bin &lt;nil&gt;
 
    // 返回软链指向的路径
    symlink := "/usr/local"
    real_dir, err = filepath.EvalSymlinks(symlink)
    fmt.Println(real_dir, err) // /usr/local &lt;nil&gt;
 
    // 返回路径最前面的卷名
    root := "/usr/local/go"
    vol := filepath.VolumeName(root)
    fmt.Println(vol) // ''
 
    // 路径分隔符替换为 `/`
    slash_dir := filepath.ToSlash(root)
    fmt.Println(slash_dir) // /usr/local/go
 
    //  `/` 替换为路径分隔符
    from_slash := filepath.FromSlash(slash_dir)
    fmt.Println(from_slash) // /usr/local/go
 
    // 分隔环境变量里面的路径
    env_path := "/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/go/bin"
    env_slice := filepath.SplitList(env_path)
    for k, v := range env_slice {
        fmt.Println(k, v)
    }
    // 0 /usr/local/bin
    // 1 /usr/bin
    // 2 /bin
    // 3 /usr/sbin
    // 4 /sbin
    // 5 /opt/X11/bin
    // 6 /usr/local/go/bin
 
    // 遍历 root 目录下的文件树，并调用 walkFn
    root_dir, err := os.Getwd()
    err = filepath.Walk(root_dir, pathName)
    fmt.Println(err)
}
```


