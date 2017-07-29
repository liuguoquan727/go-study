**Go语言基础⑦Go Slice**

[TOC]

切片是基于数组实现的，它的底层是数组，它自己本身非常小，可以理解为对底层数组的抽象。因为基于数组实现，所以它的底层的内存是连续分配的，效率非常高，还可以通过索引或迭代获得数据。

切片对象非常小，是因为它是只有3个字段的数据结构：一个是指向底层数组的指针，一个是切片的长度，一个是切片的容量。这3个字段，就是Go语言操作底层数组的元数据，有了它们，我们就可以任意的操作切片了。

# 声明和初始化

* **make方式**

使用内置的make函数时，需要传入一个参数，指定切片的长度，例子中我们使用的时5，这时候切片的容量也是5。当然我们也可以单独指定切片的容量。

```go
slice:=make([]int,5) //5是长度
slice:=make([]int,5,10) //10是容量
```

因为切片的底层是数组，所以创建切片时，如果不指定字面值的话，默认值就是数组的元素的零值。这里我们所以指定了容量是10，但是我们只能访问5个元素，因为切片的长度是5，剩下的5个元素，需要切片扩充后才可以访问。

>容量必须>=长度，我们是不能创建长度大于容量的切片的。

* **使用字面量，就是指定初始化的值**

```go
slice:=[]int{1,2,3,4,5} //不用指定[]的值
slice:=[]int{4:1}
```

* **基于现有的数组或者切片创建**

基于现有的切片或者数组创建，使用[i:j]这样的操作符即可，她表示以i索引开始，到j索引结束,截取原数组或者切片，创建而成的新切片，新切片的值包含原切片的i索引，但是不包含j索引。

```go
slice := []int{1, 2, 3, 4, 5}
slice1 := slice[:]
slice2 := slice[0:]
slice3 := slice[:5]

fmt.Println(slice1)
fmt.Println(slice2)
fmt.Println(slice3)
```

i如果省略，默认是0；j如果省略默认是原数组或者切片的长度,所以例子中的三个新切片的值是一样的。这里注意的是i和j都不能超过原切片或者数组的索引。

```go
slice := []int{1, 2, 3, 4, 5}
newSlice := slice[1:3]
newSlice[0] = 10
	
fmt.Println(slice)
fmt.Println(newSlice)
```
新的切片和原切片共用的是一个底层数组，所以当修改的时候，底层数组的值就会被改变，所以原切片的值也改变了。当然对于基于数组的切片也一样的。

# 添加元素

切片算是一个动态数组，所以它可以按需增长，我们使用内置append函数即可。append函数可以为一个切片追加一个元素，至于如何增加、返回的是原切片还是一个新切片、长度和容量如何改变这些细节，append函数都会帮我们自动处理。

```go
slice := []int{1, 2, 3, 4, 5}
newSlice := slice[1:3]
	
newSlice=append(newSlice,10)
fmt.Println(newSlice)
fmt.Println(slice)
//Output
[2 3 10]
[1 2 3 10 5]
```

append函数会智能的增长底层数组的容量，目前的算法是：容量小于1000个时，总是成倍的增长，一旦容量超过1000个，增长因子设为1.25，也就是说每次会增加25%的容量。

# 使用切片

## 访问切片

```go
slice := []int{1, 2, 3, 4, 5}
fmt.Println(slice[2]) //获取值
slice[2] = 10 //修改值
fmt.Println(slice[2]) //输出10
```

## 迭代切片

```go
//for range
slice := []int{1, 2, 3, 4, 5}
for i,v:=range slice{
	fmt.Printf("索引:%d,值:%d\n",i,v)
}

//for range
slice := []int{1, 2, 3, 4, 5}
for _,v:=range slice{ // _ 忽略索引
	fmt.Printf("值:%d\n",v)
}

//len
slice := []int{1, 2, 3, 4, 5}
for i := 0; i < len(slice); i++ {
	fmt.Printf("值:%d\n", slice[i])
}
```

# 在函数间传递切片

```go
func main() {
	slice := []int{1, 2, 3, 4, 5}
	fmt.Printf("%p\n", &slice)
	modify(slice)
	fmt.Println(slice)
}
func modify(slice []int) {
	fmt.Printf("%p\n", &slice)
	slice[1] = 10
}

0xc420082060
0xc420082080
[1 10 3 4 5]
```

这两个切片的地址不一样，所以可以确认切片在函数间传递是复制的。而我们修改一个索引的值后，发现原切片的值也被修改了，说明它们共用一个底层数组。

# 参考

[Go 切片](http://www.flysnow.org/2017/03/14/go-in-action-go-slice.html)

