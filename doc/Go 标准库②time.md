**Go 标准库介绍②time**

[TOC]

# 时间处理类型

Go 中时间处理依赖的数据类型: time.Time, time.Month, time.Weekday, time.Duration, time.Location。

## time.Time时间点

time.Time 代表一个纳秒精度的时间点。Time结构体定义如下:

```go
type Time struct {
    sec int64 // 从1年1月1日 00:00:00 UTC 至今过去的秒数
    nsec int32 // 最近一秒到下一秒过去的纳秒数
    loc *Location // 时区
}
```

```go
var t time.Time // 定义 time.Time 类型变量
t = time.Now()  // 获取当前时间
fmt.Printf("时间: %v, 时区:  %v,  时间类型: %T\n", t, t.Location(), t)
// 时间: 2017-02-22 09:06:05.816187261 +0800 CST, 时区:  Local,  时间类型: time.Time
 
// time.UTC() time 返回UTC 时区的时间
fmt.Printf("时间: %v, 时区:  %v,  时间类型: %T\n", t.UTC(), t.UTC().Location(), t)
// 时间: 2017-02-22 01:07:15.179280004 +0000 UTC, 时区:  UTC,  时间类型: time.Time

fmt.Printf("year: %d ",t.Year())
fmt.Printf("month: %d ",t.Month())
fmt.Printf("day: %d\n",t.Day())

fmt.Printf("hour: %d ",t.Hour())
fmt.Printf("minute: %d ",t.Minute())
fmt.Printf("second: %d \n",t.Second())
```

代码中应使用time.Time 类型值来保存和传递时间，而不能用指针。表示时间的变量和字段，应为time.Time类型，而不是*time.Time类型。

## time.Month月份

time.Month 代表一年中的某个月

```go
type Month int

//Month相关的常量
const (
    January Month = 1 + iota
    February
    March
    April
    May
    June
    July
    August
    September
    October
    November
    December
)
```

## time.Weekday星期

time.Weekday 代表一周的周几。

```go
type Weekday int

//Weekday相关的常量
const (
    Sunday Weekday = iota
    Monday
    Tuesday
    Wednesday
    Thursday
    Friday
    Saturday
)
```

## time.Duration时间段

time.Duration 类型代表两个时间点之间经过的纳秒数，可表示的最长时间段约为290年。

```go
type Duration int64

const (
    Nanosecond  Duration = 1
    Microsecond          = 1000 * Nanosecond
    Millisecond          = 1000 * Microsecond
    Second               = 1000 * Millisecond
    Minute               = 60 * Second
    Hour                 = 60 * Minute
)
```

## time.Location时区

Location代表一个地点，以及该地点所在的时区信息。北京时间可以使用 `Asia/Shanghai`。

```go
type Location struct {
    name string
    zone []zone
    tx   []zoneTrans
    cacheStart int64
    cacheEnd   int64
    cacheZone  *zone
}
```

# time.Time 方法

## 获取一个时间的方法

* func Now() Time {} // 当前本地时间
* func Unix(sec int64, nsec int64) Time {} // 根据时间戳返回本地时间
* func Date(year int, month Month, day, hour, min, sec, nsec int, loc *Location) Time {} // 返回指定时间

## 时间显示

* func (t Time) UTC() Time {} // 获取指定时间在UTC 时区的时间表示
* func (t Time) Local() Time {} // 以本地时区表示
* func (t Time) In(loc *Location) Time {} // 时间在指定时区的表示
* func (t Time) Format(layout string) string {} // 按指定格式显示时间

```go
// 获取指定时间在UTC 时区的时间表示
t_by_utc := t.UTC()
fmt.Println("'t.UTC': ", t_by_utc)
 
// 获取本地时间表示
t_by_local := t.Local()
fmt.Println("'t.Local': ", t_by_local)
 
// 时间在指定时区的表示
t_in := t.In(time.UTC)
fmt.Println("'t.In': ", t_in)
 
// Format
fmt.Println("t.Format", t.Format(time.RFC3339))
```

## 获取日期信息

* func (t Time) Date() (year int, month Month, day int) {} // 返回时间的日期信息
* func (t Time) Year() int {} // 返回年
* func (t Time) Month() Month {} // 月
* func (t Time) Day() int {} // 日
* func (t Time) Weekday() Weekday {} // 星期
* func (t Time) ISOWeek() (year, week int) {} // 返回年，星期范围编号
* func (t Time) Clock() (hour, min, sec int) {} // 返回时间的时分秒
* func (t Time) Hour() int {} // 返回小时
* func (t Time) Minute() int {} // 分钟
* func (t Time) Second() int {} // 秒
* func (t Time) Nanosecond() int {} // 纳秒
* func (t Time) YearDay() int {} // 一年中对应的天
* func (t Time) Location() *Location {} // 时间的时区
* func (t Time) Zone() (name string, offset int) {} // 时间所在时区的规范名和想对UTC 时间偏移量
* func (t Time) Unix() int64 {} // 时间转为时间戳
* func (t Time) UnixNano() int64 {} // 时间转为时间戳（纳秒）

## 时间比较与计算

* func (t Time) IsZero() bool {} // 是否是零时时间
* func (t Time) After(u Time) bool {} // 时间在u 之前
* func (t Time) Before(u Time) bool {} // 时间在u 之后
* func (t Time) Equal(u Time) bool {} // 时间与u 相同
* func (t Time) Add(d Duration) Time {} // 返回t +d 的时间点
* func (t Time) Sub(u Time) Duration {} // 返回 t-u
* func (t Time) AddDate(years int, months int, days int) Time {} 返回增加了给出的年份、月份和天数的时间点Time

## 时间序列化

* func (t Time) MarshalBinary() ([]byte, error) {} // 时间序列化
* func (t Time) UnmarshalBinary(data []byte) error {} // 反序列化
* func (t Time) MarshalJSON() ([]byte, error) {} // 时间序列化
* func (t Time) MarshalText() ([]byte, error) {} // 时间序列化
* func (t Time) GobEncode() ([]byte, error) {} // 时间序列化
* func (t Time) GobDecode() ([]byte, error) {} // 时间序列化


# time.Duration 方法

* func (d Duration) String() string // 格式化输出 Duration
* func (d Duration) Nanoseconds() int64 // 将时间段表示为纳秒
* func (d Duration) Seconds() float64 // 将时间段表示为秒
* func (d Duration) Minutes() float64 // 将时间段表示为分钟
* func (d Duration) Hours() float64 // 将时间段表示为小时

```go
// time.Duration 时间段
fmt.Println("time.Duration 时间段")
d = time.Duration(10000000000000)
 
fmt.Printf("'String: %v', 'Nanoseconds: %v', 'Seconds: %v', 'Minutes: %v', 'Hours: %v'\n", 
d.String(), d.Nanoseconds(), d.Seconds(), d.Minutes(), d.Hours())
```

# time.Location 方法

* func (l *Location) String() string // 输出时区名
* func FixedZone(name string, offset int) *Location // FixedZone 使用给定的地点名name和时间偏移量offset（单位秒）创建并返回一个Location
* func LoadLocation(name string) (*Location, error) // LoadLocation 使用给定的名字创建Location

```go
var local *time.Location
local, ok := time.LoadLocation("Asia/Shanghai")
fmt.Printf("%v, %T, %v\n", local, local, ok)
```

# 其他方法

* func Sleep(d Duration) // Sleep阻塞当前go线程至少d代表的时间段。d<=0时，Sleep会立刻返回。

```go
d_second := time.Second
time.Sleep(d_second)
```


