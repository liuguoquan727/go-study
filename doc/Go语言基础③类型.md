**Go语言基础③基本类型**

[TOC]

# 常量

常量的定义与变量类似，使用 const 关键字修饰，用于存储不会改变的数据。常量只可以是字符、字符串、布尔或数字类型（整数型、浮点型和复数）的值。常量不能使用 := 语法定义。一个未指定类型的常量由上下文来决定其类型。

```go
package main
 
import (
    "fmt"
)
 
// 隐式类型数值常量
const Pi = 3.14

//显示类型
const b string = "abc"
 
func main() {
    // 字符常量
    const World = "world"
 
    fmt.Println("Hello ", World)
    fmt.Println("the value of Pi is ", Pi)
}
```

# 变量

`var` 语句定义了一个变量的列表。跟函数的参数列表一样，类型在后面，`var` 语句可以定义在包或函数级别。

```go
/**
 * variable 变量相关概念
 */
 
package main
 
import "fmt"
 
var a, b *int //声明指针类型
var c, python, java bool // 定义变量，类型在后面
var x, y, s = 1, 2, "hello" // 初始化是使用表达式，则可以省略类型，变量从初始值中获得类型

//一般用于声明全局变量
var (
    d int
    f bool
    str string
)
 
func main() {
    var i int
    fmt.Println(i, c, python, java)
 
    a, b := 1, "hello" // `:=` 简洁赋值语句用在明确类型的地方，用于替代 `var` 定义
    fmt.Println(x, y, s, a, b)
}
```

## 初始化变量

变量定义可以包含初始值，每个变量对应一个。如果初始化是使用表达式，则可以省略类型，变量将从初始值中获得类型。

## 短声明变量

在函数中， := 简洁赋值语句用在明确类型的地方，用于替代 var 定义。:= 结构不能使用在函数外，函数外的每个语句都必须以关键字开始（ var 、 func 、等等）。


# 基本类型

```go
bool //布尔类型
string  //字符串
int  int8  int16  int32  int64  //整型
uint uint8 uint16 uint32 uint64 uintptr //无符号整数
byte // uint8 的别名
rune // int32 的别名, 代表一个Unicode码
float32 float64 //浮点数
complex64 (32 位实数和虚数)
complex128 (64 位实数和虚数)
```

```go
/**
 * go 数据类型
 */
 
package main
 
import (
    "fmt"
    "math"
    "math/cmplx"
)
 
// 同导入语句一样，变量的定义“打包”在一个语法块中。
var (
    ToBe   bool       = false
    MaxInt uint64     = 1&lt;&lt;64 - 1
    z      complex128 = cmplx.Sqrt(-5 + 12i)
)
 
func main() {
    const f = "%T(%v)\n"
    fmt.Printf(f, ToBe, ToBe)     // bool(false)
    fmt.Printf(f, MaxInt, MaxInt) // uint64(18446744073709551615)
    fmt.Printf(f, z, z)           // complex128((2+3i))
 
    // 变量在定义时没有明确的初始化时会赋值为零值
    var i int
    var fl float64
    var b bool
    var s string
    fmt.Printf("%v %v %v %q\n", i, fl, b, s) // 0 0 false ""
 
    // 类型推导: 在定义一个变量却并不显式指定其类型时， 变量的类型由（等号）右侧的值推导得出
    var m int
    n := m
    fmt.Printf(f, m, m) // int(0)
    fmt.Printf(f, n, n) // int(0)
 
    // 类型转换
    x, y := 3, 4
    z := math.Sqrt(float64(x*x + y*y))
    u := uint(z)
    fmt.Printf(f, x, x) // int(3)
    fmt.Printf(f, y, y) // int(4)
    fmt.Printf(f, z, z) // float64(5)
    fmt.Printf(f, u, u) // uint(5)
}
```
* int，uint 和 uintptr 类型在32位的系统上一般是32位，而在64位系统上是64位。当你需要使用一个整数类型时，你应该首选 int，仅当有特别的理由才使用定长整数类型或者无符号整数类型
* 尽可能地使用 float64，因为 math 包中所有有关数学运算的函数都会要求接收这个类型
* 你可以通过增加前缀 0 来表示 8 进制数（如：077），增加前缀 0x 来表示 16 进制数（如：0xFF），以及使用 e 来表示 10 的连乘（如： 1e3 = 1000，或者 6.022e23 = 6.022 x 1e23）。
* Go 中不允许不同类型之间的混合使用，但是对于常量的类型限制非常少，因此允许常量之间的混合使用。

## 零值

变量在定义时没有明确的初始化时会赋值为零值

* int 数值类型为 0
* bool 布尔类型为 false
* string 字符串为 “” （空字符串）

## 类型推导

在定义一个变量却并不显式指定其类型时（使用 := 语法或者 var = 表达式语法‘）， 变量的类型由（等号）右侧的值推导得出。

当右值定义了类型时，新变量的类型与其相同：

```go
var i int
j := i // j 也是一个 int
```

## 类型转换

表达式 `T(v)` 将值 v 转换为类型 T。Go 不同类型之间的项目赋值时需要显式转换。

```go
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)
```


##格式化说明符

在格式化字符串里，%d 用于格式化整数（%x 和 %X 用于格式化 16 进制表示的数字），%g 用于格式化浮点型（%f 输出浮点数，%e 输出科学计数表示法），%0d 用于规定输出定长的整数，其中开头的数字 0 是必须的。

%n.mg 用于表示数字 n 并精确到小数点后 m 位，除了使用 g 之外，还可以使用 e 或者 f，例如：使用格式化字符串 %5.2e 来输出 3.4 的结果为 3.40e+00。

## 运算符

### 二元运算符

* 按位与 &
* 按位或 |
* 按位异或 ^
* 位清除 &^：将指定位置上的值设置为 0。

### 一元运算符

* 按位补足 ^
* 位左移 <<

```
1 << 10 // 等于 1 KB
1 << 20 // 等于 1 MB
1 << 30 // 等于 1 GB
```

* 位右移 >>

### 逻辑运算符

Go 中拥有以下逻辑运算符：`==、!= 、<、<=、>、>=、&&、||、！`。

### 算术运算符

常见可用于整数和浮点数的二元运算符有` +、-、* 和 /`。

* / 对于整数运算而言，结果依旧为整数，例如：`9 / 4 -> 2`
* 取余运算符只能作用于整数：`9 % 4 -> 1`

##  随机数

```go
import (
    "fmt"
    "math/rand"
    "time"
)

func main() {
    for i := 0; i < 10; i++ {
        a := rand.Int()
        fmt.Printf("%d / ", a)
    }
    for i := 0; i < 5; i++ {
        r := rand.Intn(8)
        fmt.Printf("%d / ", r)
    }
    fmt.Println()
    timens := int64(time.Now().Nanosecond())
    rand.Seed(timens)
    for i := 0; i < 10; i++ {
        fmt.Printf("%2.2f / ", 100*rand.Float32())
    }
}
```

# 引用类型

引用类型和原始的基本类型恰恰相反，它的修改可以影响到任何引用到它的变量。在Go语言中，引用类型有slice、map、接口、函数类型以及channel。

引用类型之所以可以引用，是因为我们创建引用类型的变量，其实是一个标头值，标头值里包含一个指针，指向底层的数据结构，当我们在函数中传递引用类型时，其实传递的是这个标头值的副本，它所指向的底层结构并没有被复制传递，这也是引用类型传递高效的原因。

>本质上，我们可以理解函数的传递都是值传递，只不过引用类型传递的是一个指向底层数据的指针，所以我们在操作的时候，可以修改共享的底层数据的值，进而影响到所有引用到这个共享底层数据的变量。

```go
func main() {
	ages := map[string]int{"张三": 20}
	fmt.Println(ages)
	modify(ages)
	fmt.Println(ages)
}
func modify(m map[string]int) {
	m["张三"] = 10
}
```

# 指针类型

GO语言支持指针类型 *T，指针的指针 **T，以及包含包名前缀的 *<package>.T。

* 默认值 nil，没有 NULL 常量。* 操作符 "&" 取变量地址，"*" 透过指针访问 标对象。* 不支持指针运算，不支持 "->" 运算符，直接用 "." 访问 标成员。
* & 操作符会生成一个指向其操作数的指针
* * 操作符表示指针指向的底层值

```go
/**
 * go 语言指针
 */
 
package main
 
import (
    "fmt"
)
 
func main() {
    i, j := 42, 36
 
    p := &i //& 操作符会生成一个指向其操作数的指针。
    fmt.Println(*p) // 42 * 操作符表示指针指向的底层值。
    *p = 21
    fmt.Println(i) // 21
 
    p = &j
    *p = *p / 4
    fmt.Println(j) // 9
    mt.Println(p) //打印指针地址
}
 
```

# 结构体

一个结构体（ struct ）就是一个字段的集合，结构体字段使用点号来访问。

```go
package main
 
import (
    "fmt"
)
 
//定义结构体
type Point struct {
    X int
    Y int
}
 
var (
    p1  = Point{1, 2} //初始化 x=1 y = 2
    p2  = Point{X: 1} //初始化 x=1 y = 0
    p3  = Point{}
    pt1 = &Point{1, 2}
)
 
func main() {
    fmt.Println(Point{1, 2})
 
    p := Point{3, 4}
    p.X = 4 // 结构体字段使用点号来访问
    fmt.Println(p, p.Y)
 
    pt := &p
    pt.X = 5 // 使用隐式间接引用，直接写 `pt.X`
    (*pt).Y = 6 // 通过 `(*pt).Y` 来访问其字段 `Y`
    fmt.Println(p)
}
```

## 结构体指针

结构体字段可以通过结构体指针来访问。

```go
pt := &p
pt.X = 5 // 使用隐式间接引用，直接写 `pt.X`
(*pt).Y = 6 // 通过 `(*pt).Y` 来访问其字段 `Y`
```

## 结构体语法

* 结构体文法通过直接列出字段的值来新分配一个结构体。
* 使用 Name: 语法可以仅列出部分字段。（字段名的顺序无关。）
* 特殊的前缀 & 返回一个指向结构体的指针。

```go
var (
    p1  = Point{1, 2}
    p2  = Point{X: 1}
    p3  = Point{}
    pt1 = &amp;Point{1, 2}
)
```

# 自定义类型

Go语言支持我们自定义类型，比如刚刚上面的结构体类型，就是我们自定义的类型，这也是比较常用的自定义类型的方法。另外一个自定义类型的方法是基于一个已有的类型，就是基于一个现有的类型创造新的类型，这种也是使用`type`关键字。

```go
type Duration int64

var i Duration = 100
var j int64 = 100
```

但是本质上，他们并不是同一种类型，所以对于Go这种强类型语言，他们是不能相互赋值的。

```go
type Duration int64
var dur Duration
dur=int64(100) //error
fmt.Println(dur)
```

# 参考

[Go 类型](http://www.flysnow.org/2017/03/26/go-in-action-go-type.html)

[Go 之旅三: 复杂类型](http://ironxu.com/709)

[]()

