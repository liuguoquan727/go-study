**Go语言基础⑧Go Map**

[TOC]

Map是基于散列表来实现，就是我们常说的Hash表，所以我们每次迭代Map的时候，打印的Key和Value是无序的，**Map存储的是无序的键值对集合。**

# 声明和初始化

* **make方式**

示例中创建了一个键类型为string的，值类型为int的map，并存储一个键值对

```go
dict:=make(map[string]int)
dict["张三"] = 43
```

* **使用map字面量的方式创建和初始化map**

```go
dict := map[string]int{"张三":43}
dict := map[string]int{"张三":43,"李四":50}
dict := map[string]int{} //空Map
```
* **创建nil的Map**

nil的Map是未初始化的，所以我们可以只声明一个变量，既不能使用map字面量，也不能使用make函数分配内存。
我们是不能对nil的Map操作存储键值对的，必须要初始化后才可以，比如使用make函数,为其开启一块可以存储数据的内存，也就是初始化。

```go
var dict map[string]int
dict = make(map[string]int) //开辟内存
dict["张三"] = 43
fmt.Println(dict)
```

>Map的键可以是任何值，键的类型可以是内置的类型，也可以是结构类型，但是不管怎么样，这个键可以使用`==`运算符进行比较，所以像切片、函数以及含有切片的结构类型就不能用于Map的键了，因为他们具有引用的语义，不可比较。对于Map的值来说，就没有什么限制了，切片这种在键里不能用的，完全可以用在值里。


# 使用Map

## 访问Map

```go
dict := make(map[string]int)
dict["张三"] = 43
age := dict["张三"]
age,exists := dict["李四"]
delete(dict,"张三") //删除
```

* `age,exists := dict["李四"]`第一个返回值是键的值；第二个返回值标记这个键是否存在，这是一个boolean类型的变量，我们判断它就知道该键是否存在了。

* delete函数删除不存在的键也是可以的，只是没有任何作用。

## 遍历Map

```go
dict := map[string]int{"张三": 43}
for key, value := range dict {
	fmt.Println(key, value)
}

dict := map[string]int{"王五": 60, "张三": 43}
var names []string
for name := range dict {
	names = append(names, name)
}
sort.Strings(names) //排序
for _, key := range names { //range 一个Map的时候，也可以使用一个返回值，这个默认的返回值就是Map的键。
	fmt.Println(key, dict[key])
}
```
>range 一个Map的时候，也可以使用一个返回值，这个默认的返回值就是Map的键。


# 函数间传递Map

函数间传递Map是不会拷贝一个该Map的副本的，也就是说如果一个Map传递给一个函数，该函数对这个Map做了修改，那么这个Map的所有引用，都会同步这个修改。

```go
func main() {
	dict := map[string]int{"王五": 60, "张三": 43}
	modify(dict)
	fmt.Println(dict["张三"])
}
func modify(dict map[string]int) {
	dict["张三"] = 10
}
```

# 参考

[Go Map](http://www.flysnow.org/2017/03/23/go-in-action-go-map.html)

