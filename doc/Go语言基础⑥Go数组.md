**Go语言基础⑥数组**

[TOC]

数组是长度固定的数据类型，必须存储一段相同类型的元素，而且这些元素是连续的。数组存储的类型可以是内置类型，比如整型或者字符串，也可以是自定义的数据结构。因为是连续的，所以索引比较好计算，所以我们可以很快的索引数组中的任何数据。

# 声明和初始化

数组的声明和初始化，和其他类型差不多。声明的原则是：

1. 指明存储数据的类型。
2. 存储元素的数量，也就是数组长度。

数组一旦声明后，其元素类型和大小都不能变了，如果还需要存储更多的元素怎么办？那么只能通过创建一个新的数组，然后把原来数组的数据复制过去。

```go
var array [5]int //初始值为0
array = [5]int{1,2,3,4,5} //初始化
```

Go为我们提供了:=操作符，可以让我们在创建数组的时候直接初始化。这种简短变量声明的方式不仅适用于数组，还适用于任何数据类型，这也是Go语言中常用的方式。

```go
array:=[5]int{1,2,3,4,5}
```

有时候我们更懒，连数组的长度都不想指定，不过没有关系，使用...代替就好了，Go会自动推导出数组的长度。

```go
array:=[...]int{1,2,3,4,5}
```

假如我们只想给索引为1和3的数组初始化相应的值，其他都为0怎么做呢，直接的办法有：

```go
array:=[5]int{0,1,0,4,0}
```
还有一种更好的办法，上面讲默认初始化为零值，那么我们就可以利用这个特性，只初始化索引1和3的值

```go
array:=[5]int{1:1,3:4} //只初始化索引1和3的值
```

# 使用数组

## 索引访问

```go
array:=[5]int{1:1,3:4}
fmt.Printf("%d\n",array[1])
array[1] = 3
fmt.Printf("%d\n",array[1])
```

## 数组遍历

```go
//for
func main() {
	array := [5]int{1: 1, 3: 4}
	for i := 0; i < 5; i++ {
		fmt.Printf("索引:%d,值:%d\n", i, array[i])
	}
}
```

```go
//for range
func main() {
	array := [5]int{1: 1, 3: 4}
	for i, v := range array {
		fmt.Printf("索引:%d,值:%d\n", i, v)
	}
}
```

## 数组赋值

同样类型的数组是可以相互赋值的，不同类型的不行，会编译错误。那么什么是同样类型的数组呢？Go语言规定，必须是长度一样，并且每个元素的类型也一样的数组，才是同样类型的数组。

```go
array := [5]int{1: 1, 3: 4}
var array1 [5]int = array //success
var array2 [4]int = array1 //error
```

## 指针数组

下面创建了一个指针数组，它的元素类型是指针，并且为索引1和3都创建了内存空间，其他索引是指针的零值nil。

```go
array := [5]*int{1: new(int), 3:new(int)}
*array[1] = 1 //修改指针元素的值

//给未初始化的元素赋值
array[0] =new(int) //分配内存
*array[0] = 2 //赋值
fmt.Println(*array[0]) //取值
```

# 函数间传递数组

## 数组传递

>不改变原数组的值

```go
func main() {
	array := [5]int{1: 2, 3:4}
	modify(array)
	fmt.Println(array)
}
func modify(a [5]int){
	a[1] =3
	fmt.Println(a)
}
```

## 数组指针传递

>数组与原数组会同时改变

```go
func main() {
	array := [5]int{1: 2, 3:4}
	modify(&array)
	fmt.Println(array)
}
func modify(a *[5]int){
	a[1] =3
	fmt.Println(*a)
}
```

# 参考

[Go 数组](http://www.flysnow.org/2017/03/13/go-in-action-go-array.html)


