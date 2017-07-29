**Go语言基础⑨Go接口和嵌入类型**

[TOC]

# 接口

接口是一种约定，它是一个抽象的类型，和我们见到的具体的类型如int、map、slice等不一样。具体的类型，我们可以知道它是什么，并且可以知道可以用它做什么；但是接口不一样，接口是抽象的，它只有一组接口方法，我们并不知道它的内部实现，所以我们不知道接口是什么，但是我们知道可以利用它提供的方法做什么。

抽象就是接口的优势，它不用和具体的实现细节绑定在一起，我们只需定义接口，告诉编码人员它可以做什么，这样我们可以把具体实现分开，这样编码就会更加灵活方面，适应能力也会非常强。

```go
func main() {
	var b bytes.Buffer
	fmt.Fprint(&b,"Hello World")
	fmt.Println(b.String())
}
```

以上就是一个使用接口的例子，我们先看下fmt.Fprint函数的实现。

```go
func Fprint(w io.Writer, a ...interface{}) (n int, err error) {
	p := newPrinter()
	p.doPrint(a)
	n, err = w.Write(p.buf)
	p.free()
	return
}
```

从上面的源代码中，我们可以看到，`fmt.Fprint`函数的第一个参数是`io.Writer`这个接口，所以只要实现了这个接口的具体类型都可以作为参数传递给fmt.Fprint函数，而bytes.Buffer恰恰实现了`io.Writer`接口，所以可以作为参数传递给`fmt.Fprint`函数。

## 内部实现

我们前面提过接口是用来定义行为的类型，它是抽象的，这些定义的行为不是由接口直接实现，而是通过方法由用户定义的类型实现。如果用户定义的类型，实现了接口类型声明的所有方法，那么这个用户定义的类型就实现了这个接口，所以这个用户定义类型的值就可以赋值给接口类型的值。

赋值操作执行后，如果我们对接口方法执行调用，其实是调用存储的用户定义类型的对应方法，这里我们可以把用户定义的类型称之为**实体类型**。

我们可以定义很多类型，让它们实现一个接口，那么这些类型都可以赋值给这个接口，这时候接口方法的调用，其实就是对应实体类型对应方法的调用，这就是多态。

```go
func main() {
	var a animal
	var c cat
	a=c
	a.printInfo()
	//使用另外一个类型赋值
	var d dog
	a=d
	a.printInfo()
}
type animal interface {
	printInfo()
}
type cat int
type dog int
func (c cat) printInfo(){
	fmt.Println("a cat")
}
func (d dog) printInfo(){
	fmt.Println("a dog")
}

a cat
a cat
a dog
a dog
```

以上例子演示了一个多态。我们定义了一个接口animal,然后定义了两种类型cat和dog实现了接口animal。在使用的时候，分别把类型cat的值c、类型dog的值d赋值给接口animal的值a,然后分别执行a的printInfo方法，可以看到不同的输出。

>接口的值是一个两个字长度的数据结构，第一个字**包含一个指向内部表结构的指针**，这个内部表里存储的有实体类型的信息以及相关联的方法集；第二个字**包含的是一个指向存储的实体类型值的指针**。所以接口的值结构其实是两个指针，这也可以说明接口其实一个引用类型。

## 方法集

我们都知道，如果要实现一个接口，必须实现这个接口提供的所有方法，但是实现方法的时候，我们可以使用指针接收者实现，也可以使用值接收者实现，这两者是有区别的，下面我们就好好分析下这两者的区别。

```go
func main() {
	var c cat
	//值作为参数传递
	invoke(c)
}
//需要一个animal接口作为参数
func invoke(a animal){
	a.printInfo()
}
type animal interface {
	printInfo()
}
type cat int
//值接收者实现animal接口
func (c cat) printInfo(){
	fmt.Println("a cat")
}
```

还是原来的例子改改，增加一个invoke函数，该函数接收一个animal接口类型的参数，例子中传递参数的时候，也是以类型cat的值c传递的，运行程序可以正常执行。现在我们稍微改造一下，使用类型cat的指针&c作为参数传递。

```go
func main() {
	var c cat
	//指针作为参数传递
	invoke(&c)
}
```

只修改这一处，其他保持不变，我们运行程序，发现也可以正常执行。通过这个例子我们可以得出结论：**实体类型以值接收者实现接口的时候，不管是实体类型的值，还是实体类型值的指针，都实现了该接口**。

下面我们把接收者改为指针试试。

```go
func main() {
	var c cat
	//值作为参数传递
	invoke(c)
}
//需要一个animal接口作为参数
func invoke(a animal){
	a.printInfo()
}
type animal interface {
	printInfo()
}
type cat int
//指针接收者实现animal接口
func (c *cat) printInfo(){
	fmt.Println("a cat")
}
```

这个例子中把实现接口的接收者改为指针，但是传递参数的时候，我们还是按值进行传递，点击运行程序，会出现以下异常提示：

```
./main.go:10: cannot use c (type cat) as type animal in argument to invoke:
	cat does not implement animal (printInfo method has pointer receiver)
```

提示中告诉我们，说cat没有实现animal接口，因为printInfo方法有一个指针接收者，所以cat类型的值c不能作为接口类型animal传参使用。下面我们再稍微修改下，改为以指针作为参数传递。

```go
func main() {
	var c cat
	//指针作为参数传递
	invoke(&c)
}
```

其他都不变，只是把以前使用值的参数，改为使用指针作为参数，我们再运行程序，就可以正常运行了。由此可见**实体类型以指针接收者实现接口的时候，只有指向这个类型的指针才被认为实现了该接口**。

# 嵌入类型

嵌入类型，或者嵌套类型，这是一种可以把已有的类型声明在新的类型里的一种方式，这种功能对代码复用非常重要。

在其他语言中，有继承可以做同样的事情，但是在Go语言中，没有继承的概念，Go提倡的代码复用的方式是组合，所以这也是嵌入类型的意义所在，组合而不是继承，所以Go才会更灵活。

```go
type Reader interface {
	Read(p []byte) (n int, err error)
}
type Writer interface {
	Write(p []byte) (n int, err error)
}
type Closer interface {
	Close() error
}
type ReadWriter interface {
	Reader
	Writer
}
type ReadCloser interface {
	Reader
	Closer
}
type WriteCloser interface {
	Writer
	Closer
}
```

以上是标准库io包里，我们常用的接口，可以看到ReadWriter接口是嵌入Reader和Reader接口而组合成的新接口，这样我们就不用重复的定义被嵌入接口里的方法，直接通过嵌入就可以了。嵌入类型同样适用于结构体类型，我们再来看个例子：

```go
type user struct {
	name string
	email string
}
type admin struct {
	user
	level string
}
```

嵌入后，被嵌入的类型称之为内部类型、新定义的类型称之为外部类型，这里user就是内部类型，而admin是外部类型。

通过嵌入类型，与内部类型相关联的所有字段、方法、标志符等等所有，都会被外包类型所拥有，就像外部类型自己的一样，这就达到了代码快捷复用组合的目的，而且定义非常简单，只需声明这个类型的名字就可以了。

同时，外部类型还可以添加自己的方法、字段属性等，可以很方便的扩展外部类型的功能。

```go
func main() {
	ad:=admin{user{"张三","zhangsan@flysnow.org"},"管理员"}
	fmt.Println("可以直接调用,名字为：",ad.name)
	fmt.Println("也可以通过内部类型调用,名字为：",ad.user.name)
	fmt.Println("但是新增加的属性只能直接调用，级别为：",ad.level)
}
```

以上是嵌入类型的使用，可以看到，我们在初始化的时候，采用的是字面值的方式，所以要按其定义的结构进行初始化，先初始化user这个内部类型的，再初始化新增的level 属性。

对于内部类型的属性和方法访问上，我们可以用外部类型直接访问，也可以通过内部类型进行访问；但是我们为**外部类型新增的方法属性字段，只能使用外部类型访问，因为内部类型没有这些**。

当然，外部类型也可以声明同名的字段或者方法，来覆盖内部类型的，这种情况方法比较多，我们以方法为例

```go
func main() {
	ad:=admin{user{"张三","zhangsan@flysnow.org"},"管理员"}
	ad.user.sayHello()
	ad.sayHello()
}
type user struct {
	name string
	email string
}
type admin struct {
	user
	level string
}
func (u user) sayHello(){
	fmt.Println("Hello，i am a user")
}
func (a admin) sayHello(){
	fmt.Println("Hello，i am a admin")
}
```
内部类型user有一个sayHello方法，外部类型对其进行了覆盖，同名重写sayHello，然后我们在main方法里分别访问这两个类型的方法，打印输出:

```go
Hello，i am a user
Hello，i am a admin
```

嵌入类型的强大，还体现在：**如果内部类型实现了某个接口，那么外部类型也被认为实现了这个接口**。我们稍微改造下例子看下。

```go
func main() {
	ad:=admin{user{"张三","zhangsan@flysnow.org"},"管理员"}
	sayHello(ad.user)//使用user作为参数
	sayHello(ad)//使用admin作为参数
}
type Hello interface {
	hello()
}
func (u user) hello(){
	fmt.Println("Hello，i am a user")
}
func sayHello(h Hello){
	h.hello()
}
```

实现这个接口，最后我们定义了一个sayHello方法，它接受一个Hello接口类型的参数，最终我们在main函数演示的时候，发现不管是user类型，还是admin类型作为参数传递给sayHello方法的时候，都可以正常调用。

这里就可以说明admin实现了接口Hello,但是我们又没有显示的声明类型admin实现，所以这个实现是通过内部类型user实现的，因为admin包含了user所有的方法函数，所以也就实现了接口Hello。

当然外部类型也可以重新实现，只需要像上面例子一样覆盖同名的方法即可。这里要说明的是，不管我们如何同名覆盖，都不会影响内部类型，我们还可以通过访问内部类型来访问它的方法、属性字段等。

