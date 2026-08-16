---
name: knl-lan-go
description: 当用户想要了解 go 语法，这篇文档可能可以提供帮助
disable-model-invocation: true
---

# knl-lan-go

此文主要讲解了 go 语法的一部分容易遗忘的知识内容

## 基本

Go 为了解决 C++ 编译慢，Java 运行臃肿。官方定义 Simple, Fast, Reliable

Go Modules 是 Go 语言的官方依赖管理工具。go.mod 该文件定义了模块的路径、Go 版本要求和依赖关系，Go 工具会自动解析和维护。在 Go Modules 出现之前，Go 使用 GOPATH 模式。go.sum 文件记录依赖模块的加密哈希值，用于验证模块内容的完整性。

go.mod 主要包含：
```go
// 模块路径
module example.com/mymodule
// Go 版本要求
go 1.21
// 依赖关系
require (
    github.com/gin-gonic/gin v1.9.1
    golang.org/x/text v0.12.0
)
// 本地替换
replace golang.org/x/text => ../local/text
// 排除特定版本
exclude github.com/old/module v1.0.0
```

go mod 初始化和依赖管理
```shell
# 在现有项目中初始化
cd /path/to/project
go mod init

# 添加依赖（自动选择最新版本）
go get github.com/gin-gonic/gin

# 更新所有依赖
go get -u all

# 编写代码并导入依赖，然后运行以下命令自动处理依赖
go mod tidy
```

基本：
```shell
# 执行 go 代码
go run hello.go

# 生成二进制文件，然后再执行
go build hello.go
./hello
```

## 数据类型和变量

类型:

```go
/*
整型
uint8, uint16, uint32, uint64
int8, int16, int32, int64
byte 类似 uint8
rune 类似 int32
uint 类似 uint32 或 uint64
int 与 uint 类型的大小相同，根据平台而定
*/

/*
浮点
float32, float64
complex64, complex128
*/

// 类型转换
var a int = 10
var b float64 = float64(a)
```

变量:
如果没有初始化，则变量默认为零值（或 nil）

```go
// 如下默认 nil
var a *int
var a []int
var a map[string] int
var a chan int
var a func(string) int
var a error

// 变量声明，可简写成 := 语句
var a string = "hello"
a := "hello"

// 常量
const LENGTH int = 10

// go 无枚举关键字，用 const 常量来做即可
const (
    Unknown = 0
    Female  = 1
    Male    = 2
)
```

数组:

```go
var numbers = [5]int{1, 2, 3, 4, 5}
```

指针:

```go
var a int= 20
var ip *int
// 指针指向变量 a 的地址
ip = &a
fmt.Printf("ip 变量储存的指针地址: %x\n", ip )
fmt.Printf("*ip 变量的值: %d\n", *ip )
```

## 条件

Go 没有三目运算符

## 循环

```go
// 无限循环
package main
import "fmt"
func main() {
    for true  {
    }
}

// range 遍历数组，i 是索引，v 是值
var pow = []int{1, 2, 4, 8, 16, 32, 64, 128}
for i, v := range pow {
}

// range 遍历字符串
for i, v := range "hello" {
}

// range 遍历 map
map1 := make(map[int]float32)
map1[1] = 1.0
map1[2] = 2.0
for key, value := range map1 {
}

// 只用读 key
for key := range map1 {
}

// 只读 value
for _, value := range map1 {
}

// range 从 chan 通道接受内容
ch := make(chan int, 2)
ch <- 1
ch <- 2
close(ch)
for v := range ch {
}
```

## 函数

函数支持返回多个值

```go
func max(num1, num2 int) int {
}

// 函数返回多个值
func swap(x, y string) (string, string) {
   return y, x
}
```

## 结构体

```go
type Books struct {
   title string
   author string
   subject string
   book_id int
}

// 继承，使用组合的方式
type Student struct {
    Books
    score int
}
```

## slice 切片

数组和切片有区别，直接看如下：

```go
// 数组
var powArray = [8]int{1, 2, 4, 8, 16, 32, 64, 128}
// 切片
var powSlice = []int{1, 2, 4, 8, 16, 32, 64, 128}
```

Go 语言切片是对数组的抽象。Go 数组的长度不可改变，切片("动态数组")，可以追加元素，所以一般用的非常多

```go
// 切片 len() 3, cap() 5，make() 初始化切片，cap 为可选参数
var numbers = make([]int, 3, 5)

// 切片切割，左闭右开
fmt.Println("numbers[1:4] ==", numbers[1:4])

// append() 追加元素
numbers = append(numbers, 9)
numbers = append(numbers, 10, 11)

// copy() 复制切片，浅拷贝，指针关联
copy(numbers, numbers[1:4])
fmt.Println("numbers ==", numbers)

// copy() 数组时候，是深拷贝，副本数组
```

## map
```go
// 声明
m := make(map[string]int)
m := map[string]int{
    "apple": 1,
    "banana": 2,
    "orange": 3,
}

// 获取，如果键不存在，ok 为 false
v, ok := m["pear"]

// len(m) 获取长度

// 删除元素
delete(myMap, "France")
```

## 接口与实现
```go
// 接口
type Shape interface {
    Area() float64
    Perimeter() float64
}

// 实现类
type Circle struct {
    Radius float64
}
// Circle 实现 Shape 接口
func (c Circle) Area() float64 {
    return math.Pi * c.Radius * c.Radius
}
func (c Circle) Perimeter() float64 {
    return 2 * math.Pi * c.Radius
}

// main
func main() {
    c := Circle{Radius: 5}
    // 接口变量可以存储实现了接口的类型
    var s Shape = c
    fmt.Println("Area:", s.Area())
    fmt.Println("Perimeter:", s.Perimeter())
}
```

## 异常处理
```go
// 返回异常的函数
func divide(a, b int) (int, error) {
    if b == 0 {
        // 异常创建
        return 0, errors.New("division by zero")
    }
    return a / b, nil
}
```

## goroutine 协程
使用 go 语句开启一个新的运行期线程，以一个不同的、新创建的 goroutine 来执行一个函数。同一个程序中的所有 goroutine 共享同一个地址空间

```go
func main() {
    go sayHello() // 启动 Goroutine
    for i := 0; i < 5; i++ {
        fmt.Println("Main")
        time.Sleep(100 * time.Millisecond)
    }
}
```

## chan 通道
用于 Goroutine 之间的数据传递。make 函数创建通道，<- 操作符发送和接收数据，如果未指定方向，则为双向通道

```go
// 创建通道
ch := make(chan int)
// 发送数据，1 发送到通道 ch 中
ch <- 1
// 接收数据，v 从 ch 中接受数据
v := <-ch

// 通道缓冲区
ch := make(chan int, 100)

// 通道是否关闭，ok 为 false 表示通道已关闭
_, ok := <-ch
if !ok {
    fmt.Println("通道已关闭")
}

// select 语句，使得一个 goroutine 等待多个通道上的操作，直到有一个 case 可以执行，否则 select 阻塞
select {
case v := <-ch:
    fmt.Println("通道 ch 接收数据:", v)
case ch <- 2:
    fmt.Println("通道 ch 发送数据:", 2)
}

// WaitGroup 等待多个 goroutine 完成，再继续执行
var wg sync.WaitGroup
// 增加计数器，启动了2个goroutine，所以加2
wg.Add(2)
// 启动 goroutine 1
go func() {
    defer wg.Done() // Goroutine 完成时调用 Done()
    fmt.Println("Goroutine 1")
}()
// 启动 goroutine 2
go func() {
    defer wg.Done() // Goroutine 完成时调用 Done()
    fmt.Println("Goroutine 2")
}()
// 等待 goroutine 1 和 goroutine 2 完成
wg.Wait()
```

