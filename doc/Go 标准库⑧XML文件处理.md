[TOC]

# 示例

## XML文件

```xml
<?xml version="1.0" encoding="utf-8" ?>
<servers version="1">
    <server>
        <serverName>Shanghai_VPN</serverName>
        <serverIP>192.168.1.2</serverIP>
        <port>8080</port>
    </server>
    <server>
        <serverName>Beijing_VPN</serverName>
        <serverIP>192.168.1.3</serverIP>
        <port>8080</port>
    </server>
</servers>

```

## XML处理

```go
package main

import (
	"os"
	"fmt"
	"encoding/xml"
)

type ServerHost struct {
	ServerName string `xml:"serverName"` //序列化xml字段名为serverName
	ServerIp string `xml:"serverIP"`
	Port int `xml:"port"`
}

type Servers struct {
	XMLName xml.Name `xml:"servers"` //
	Version string `xml:"version,attr"`
	ServerHosts []ServerHost `xml:"server"`
}

func main() {

	var s Servers

	path := "/Users/liuguoquan/go/src/liu/basic/liu.xml"
	fin, err := os.Open(path)

	defer fin.Close() //延迟关闭资源
	if err != nil {
		fmt.Println(err)
		return
	}
	decoder := xml.NewDecoder(fin)
	//xml反序列化
	decoder.Decode(&s)

	fmt.Println("Version:" +s.Version)
	for i := 0; i < len(s.ServerHosts);i++ {
	   fmt.Println(s.ServerHosts[i].ServerName,s.ServerHosts[i].ServerIp,s.ServerHosts[i].Port)
	}
	fmt.Println()

	server1 := ServerHost {ServerName:"liu",ServerIp:"192.168.1.12",Port:8888}
	server2 := ServerHost {ServerName:"lee",ServerIp:"192.168.1.13",Port:8888}
	s2 := Servers { Version:"1" }
	s2.ServerHosts = append(s2.ServerHosts,server1)
	s2.ServerHosts = append(s2.ServerHosts,server2)
	//xml序列化
	out,err := xml.MarshalIndent(s2,"","  ")

	if err != nil {

	}
	fmt.Print(xml.Header)
	fmt.Println(string(out))
}

/**
Version:1
Shanghai_VPN 192.168.1.2 8080
Beijing_VPN 192.168.1.3 8080

<?xml version="1.0" encoding="UTF-8"?>
<servers version="1">
  <server>
    <serverName>liu</serverName>
    <serverIP>192.168.1.12</serverIP>
    <port>8888</port>
  </server>
  <server>
    <serverName>lee</serverName>
    <serverIP>192.168.1.13</serverIP>
    <port>8888</port>
  </server>
</servers>
 */
```



