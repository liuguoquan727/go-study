**Go语言基础④流程控制**

[TOC]

# for

Go 只有一种循环结构：for 循环。基本的 for 循环由三部分组成，用分号隔开：

* 初始化语句：在第一次迭代前执行
* 条件表达式：在每次迭代前求值
* 后置语句：在每次迭代的结尾执行

初始化语句通常为一句短变量声明，该变量声明仅在 for 语句的作用域中可见。Go 的 for 语句后面没有小括号，大括号 { } 则是必须的。

```go
package main

import "fmt"

func main() {

	sum := 0
	for i := 0; i < 10; i ++ {
		sum += i
	}
	fmt.Println("sum:", sum) // sum: 45

	// 初始化语句和后置语句是可选的
	sum = 1
	for ; sum < 1000; {
		sum += sum
	}
	fmt.Println("sum:", sum) // sum: 1024

	// 去掉分号 `;`, `for` 是 Go 中的 `while`
	sum = 1
	for sum < 1000 {
		sum += sum
	}
	fmt.Println("sum:", sum) // sum: 1024
	
	//无限循环
	for {
	
    }
}

```

# if else

```go
/**
 * go if 控制语句
 */
 
package main
 
import (
    "fmt"
    "math"
)
 
func sqrt(x float64) string {
    // 表达式外无需小括号
    if x <= 0 {
        return sqrt(-x) + "i"
    }
    return fmt.Sprint(math.Sqrt(x))
}
 
func pow(x, n, lim float64) float64 {
    // 条件表达式前可以执行一个简单的语句
    if v := math.Pow(x, n); v < lim {
        return v
    } else {
        // 表达式前执行简单的语句，语句声明的变量作用域仅在 if 和对应的 else 块中使用
        fmt.Printf("%g &gt;= %g\n", v, lim)
    }
    return lim
}
 
func main() {
    fmt.Println(sqrt(2), sqrt(-4))
 
    fmt.Println(
        pow(3, 2, 10),
        pow(3, 3, 20),
    )
}
```

Go 的 if 语句与 for 循环类似，表达式外无需小括号 ( ) ，而大括号 { } 则是必须的。

# switch

```go
/**
 * go switch 控制语句
 */
 
package main
 
import (
    "fmt"
    "time"
)
 
func main() {
    switch i := 1; i {
    case 0:
        fmt.Println(0)
    case 1:
        fmt.Println(1)
        fallthrough // 继续运行下面的代码，否则终止
    default:
        fmt.Println(2)
    }
    // 以上代码段输出
    // 1
    // 2
 
    // 没有条件的 switch
    t := time.Now()
    switch {
    case t.Hour() < 12:
        fmt.Println("早上好!")
    case t.Hour() < 17:
        fmt.Println("下午好!")
    default:
        fmt.Println("晚上好!")
    }
}
```

如果在执行完每个分支的代码后，还希望继续执行后续分支的代码，可以使用 fallthrough 关键字来达到目的。。switch 的 case 语句从上到下顺次执行，直到匹配成功时停止。

没有条件的 switch 同 switch true 一样，这种形式能将一长串 if-then-else 写得更加清晰

# defer

defer 语句会将函数推迟到外层函数返回之后执行，通常用于释放资源或错误处理。推迟调用的函数其参数会立即求值，但直到外层函数返回前该函数都不会被调用。推迟的函数调用会被压入一个栈中。 当外层函数返回时，被推迟的函数会按照后进先出的顺序调用。

```go

package main

import "fmt"

func print() {
	defer fmt.Println("world")
	fmt.Println("hello")
}

func main() {
	print()

	fmt.Println("counting")
	for i := 0; i < 10; i++ {
		defer fmt.Println(i)
	}
	fmt.Println("done")
	//hello 
	// world
	// counting
	// done
	// 9
	// 8
	// 7
	// 6
	// 5
	// 4
	// 3
	// 2
	// 1
	// 0
}
```

# Break 与 continue

* break 语句的作用结果是跳过整个代码块，执行后续的代码。
* continue 跳过本次循环的语句而直接进入下一次循环的过程。
* 关键字 break 可用于 for、switch、select 语句中，关键字 continue 仅能用于 for 循环中。

