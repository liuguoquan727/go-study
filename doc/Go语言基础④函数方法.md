**Go语言基础⑤函数方法**

[TOC]

# 函数

函数和方法，虽然概念不同，但是定义非常相似。函数的定义声明没有接收者，所以我们直接在go文件里，go包之下定义声明即可。

```go
**
 * function 函数相关概念
 */
 
package main
 
import (
    "fmt"
)
 
// 数字相加
func Add(x int, y int) int {
    return x + y
}

func substract(x int, y int) int {
    return x - y
}
 
// 字符交换位置  多值返回
func swap(x, y string) (string, string) {
    return y, x
}
 
// 命名返回值
func split(sum int) (x, y int) {
    x = sum * 4 / 9
    y = sum - x
    return
}
```

## 函数参数

* 函数可以没有参数或接受多个参数。add 函数接受两个 int 类型的参数。需要注意类型在变量名后面。
* 当两个或多个连续的函数命名参数是同一类型，则除了最后一个类型之外，其他都可以省略。

```
x string, y string
x, y string
```

## 多值返回

函数可以返回任意数量的返回值。swap 函数返回了两个字符串。

* 不能用容器对象接收多返回值。
* 多返回值可直接作为其他函数调用的实参。

## 命名返回值

Go 的返回值可以被命名，并且就像在函数体开头声明的变量那样使用。返回值的名称应当具有一定的意义，可以作为文档使用。没有参数的 return 语句返回各个返回变量的当前值。

## 参数列表

参数列表本质上就是 slice ，只能有一个，且必须是最后一个。

```go
func test(s string, n ...int) string {    var x int    for _, i := range n {        x += i}    return fmt.Sprintf(s, x)}func main() {    println(test("sum: %d", 1, 2, 3))}
```

## 作用域

例子中的函数名称是小写开头的，它的作用域只属于所声明的包内使用，不能被其他包使用，如果我们把函数名以大写字母开头，该函数的作用域就大了，可以被其他包调用。这也是Go语言中大小写的用处。

# 方法

方法的声明和函数类似，他们的区别是：方法在定义的时候，会在func和方法名之间增加一个参数，这个参数就是接收者，这样我们定义的这个方法就和接收者绑定在了一起，称之为这个接收者的方法。

```go
type person struct {
	name string
}
func (p person) String() string{
	return "the person name is "+p.name
}

func main() {
	p:=person{name:"张三"}
	fmt.Println(p.String())
}
```
func和方法名之间增加的参数`(p person)`,这个就是接收者。

## 值接收者

使用值类型接收者定义的方法，在调用的时候，使用的其实是值接收者的一个副本，所以对该值的任何操作，不会影响原来的类型变量。

```go
func main() {
	p:=person{name:"张三"}
	p.modify() //值接收者，修改无效
	fmt.Println(p.String())
}
type person struct {
	name string
}
func (p person) String() string{
	return "the person name is "+p.name
}
func (p person) modify(){
	p.name = "李四"
}
```


## 指针接收者

如果我们使用一个指针作为接收者，那么就会其作用了，因为指针接收者传递的是一个指向原值指针的副本，指针的副本，指向的还是原来类型的值，所以修改时，同时也会影响原来类型变量的值。

```go
func main() {
	p:=person{name:"张三"}
	p.modify() //指针接收者，修改有效 == (&p).modify() 
	fmt.Println(p.String())
}
type person struct {
	name string
}
func (p person) String() string{
	return "the person name is "+p.name
}
func (p *person) modify(){
	p.name = "李四"
}
```
>在调用方法的时候，传递的接收者本质上都是副本，只不过一个是这个值副本，一是指向这个值指针的副本。指针具有指向原有值的特性，所以修改了指针指向的值，也就修改了原有的值。我们可以简单的理解为值接收者使用的是值的副本来调用方法，而指针接收者使用实际的值来调用方法。

# 参考

[Go 函数方法](http://www.flysnow.org/2017/03/31/go-in-action-go-method.html)


