[TOC]

GO语言标准库`encoding/json`用于处理JSON文本数据。

# 序列化

将一个对象编码成JSON数据，接受一个interface{}对象，返回[]byte和error：

```
func Marshal(v interface{}) ([]byte, error)
```
Marshal函数将会递归遍历整个对象，依次按成员类型对这个对象进行编码，类型转换规则如下：

* bool类型 转换为JSON的Boolean
* 整数、浮点数等数值类型 转换为JSON的Number
* string 转换为JSON的字符串(带""引号)
* struct 转换为JSON的Object，再根据各个成员的类型递归打包
* 数组或切片 转换为JSON的Array
* []byte 会先进行base64编码然后转换为JSON字符串
* map 转换为JSON的Object，key必须是string
* interface{} 按照内部的实际类型进行转换
* nil 转为JSON的null
* channel,func等类型 会返回UnsupportedTypeError

# 反序列化

将JSON数据解析为Go结构体

```
func Unmarshal(data []byte, v interface{}) error
```
interface{}类型在Unmarshal时，会自动将JSON转换为对应的数据类型：

* JSON的boolean 转换为bool
* JSON的数值 转换为float64
* JSON的字符串 转换为string
* JSON的Array 转换为[]interface{}
* JSON的Object 转换为map[string]interface{}
* JSON的null 转换为nil

* 结构体必须是大写字母开头的成员才会被JSON处理到，小写字母开头的成员不会有影响。
* Unmashal时，会自动匹配对应的变量名进行赋值，大小写不敏感。
* Unmarshal时，如果JSON中有多余的字段，会被直接抛弃掉；如果JSON缺少某个字段，则直接忽略不对结构体中变量赋值，不会报错。

# StructTag

如果希望手动配置结构体的成员和JSON字段的对应关系，可以在定义结构体的时候给成员打标签

```
type Message struct {  
    Name string `json:"msg_name"`       // 对应JSON的msg_name  
    Body string `json:"body,omitempty"` // 如果为空置则忽略字段  
    Time int64  `json:"-"`              // 直接忽略字段  
}  
```

# 示例

## JSON文件

```json
{
  "servers":[
    {
      "serverName":"Shanghai_VPN",
      "serverIp":"127.0.0.1",
      "port":8080
    },
    {
      "serverName":"Beijing_VPN",
      "serverIp":"127.0.0.2",
      "port":8080
    }
  ]
}
```

## JSON序列化和反序列化

```go
package main

import (
	"os"
	"fmt"
	"encoding/json"
)

type Server struct {
	ServerName string `json:"serverName"` //序列化json字段名为serverName
	ServerIp string `json:"serverIp"`
	Port int `json:"port"`
}

type ServerSlice struct {
	Servers []Server `json:"servers"`
}

func main() {

	var s ServerSlice
	var count int

	path := "/Users/liuguoquan/go/src/liu/basic/123.json"
	fin, err := os.Open(path)

	defer fin.Close() //延迟关闭资源
	if err != nil {
		fmt.Println(err)
		return
	}
	buf := make([]byte, 1024) //创建一个初始容量为1024的slice,作为缓冲容器
	for {
		//循环读取文件数据到缓冲容器中,返回读取到的个数
		n, _ := fin.Read(buf)
		count += n
		if 0 == n {
			break //如果读到个数为0,则读取完毕,跳出循环
		}
		//从缓冲slice中写出数据,从slice下标0到n,通过os.Stdout写出到控制台
		//os.Stdout.Write(buf[:n])
	}
	fmt.Println(count)
	input := buf[0:count]
	//json反序列化
	err = json.Unmarshal(input, &s)
	if err != nil {
		fmt.Println(err)
	}
	for i := 0; i < len(s.Servers);i++ {
		fmt.Println(s.Servers[i].ServerName,s.Servers[i].ServerIp,s.Servers[i].Port)
	}

	server1 := Server {ServerName:"liu",ServerIp:"192.168.1.12",Port:8888}
	server2 := Server {ServerName:"lee",ServerIp:"192.168.1.13",Port:8888}
	s2 := ServerSlice { Servers:[]Server{server1,server2} }
	//json序列化
	out,err := json.Marshal(s2)

	if err != nil {

	}
	os.Stdout.Write(out)
}

//打印
Shanghai_VPN 127.0.0.1 8080
Beijing_VPN 127.0.0.2 8080
{"servers":[{"serverName":"liu","serverIp":"192.168.1.12","port":8888},{"serverName":"lee","serverIp":"192.168.1.13","port":8888}]}

```

