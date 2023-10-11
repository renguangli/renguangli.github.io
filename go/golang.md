

## 环境搭建

1. 下载 sdk，解压

   下载地址：https://go.dev/dl/，这里下载 1.17.5 版本

   ```
   cd /Users/renguangli/go/sdk
   wget https://go.dev/dl/go1.17.5.darwin-arm64.tar.gz
   tar -xf go1.17.5.darwin-arm64.tar.gz
   ```

2. 配置配置环境变量

   GOROOT go安装路径
   GOPATH go源代码路径

   ```
   vi ~/.zsh_profile
   export GOROOT=/Users/renguangli/go/sdk/go1.17.5
   export PATH=$PATH:$GOROOR/bin
   source ~/.zsh_profile
   ```

3. GOPROXY  /  GO111MODULE

```
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct
```

## go 常用命令

- build 打包
- clean 移除 go build 生成的二进制文件
- doc 显示包或者符号的文档
- env 打印 got 的环境信息
- bug 启动错误报告
- fix 运行 go tool fix
- generate 从processiong source 生成 go 文件
- get 下载并安装包个依赖
- install 编译编译并安装包和依赖
- list 列出包
- run 编译并运行go程序
- test 运行测试
- tool 运行go提供的工具
- version 显示go的版本

## hello go

```
package main

import "fmt"

func main(){
    fmt.Print("hello go")
}
```



## 变量与常量

### 变量

### 常量

## 标识符关键字命名规范

### 标识符

英文 identifier ，其实就是给变量，常量，函数，方法，结构体，数组，切片，接口起名

### 标识符的组成

1. 标识符由数字，字符，下划线(_)组成。
2. 标识符只能以字母或下划线组成。
3. 标识符是区分大小写的。

例子：

```
name,age,user_name,email11,_phone
```

错误例子

```
1name,123
```

### 关键字

go 提供了25个关键字

| break    | default     | func   | interface | select |
| -------- | ----------- | ------ | --------- | ------ |
| case     | defer       | go     | map       | struct |
| chan     | else        | goto   | package   | switch |
| const    | fallthrough | if     | range     | type   |
| continue | for         | import | return    | var    |

除了25 个关键关键字意外，go还提供了36个预定义标识符，包含基本类型和函数名称

| int     | int8    | int16   | int32     | int64      | bool     |
| ------- | ------- | ------- | --------- | ---------- | -------- |
| unint   | uint8   | uint16  | uint32    | uint64     | uintpstr |
| float32 | float64 | complex | complex64 | complex128 | string   |
| nil     | true    | false   | iota      | byte       | real     |
| panic   | recover | new     | make      | copy       | close    |
| append  | len     | cap     | print     | printf     | imag     |

> 关键字不能定义为标识符

### 标识符命名规范

1. 变量命名命名必须清晰、明了，有明确含义的单词，命名中禁止使用缩写，除非已是业界通用或标准化的缩写；
   单字母名称仅适用于短方法中的局部变量，名称长短应与其作用域相对应。若变量或常量可能在代码中多处使用，则应赋其以便于搜索的名称且有意义的名称。
   变量名称一般遵循驼峰法，但遇到特有名词时，需要遵循以下规则：如果变量为私有，且特有名词为首个单词，则使用小写，如 apiClient，其它情况都应当使用该名词原有的写法，如APIClient、repoID、UserID
   错误示例：UrlArray，应该写成urlArray或者URLArray
   一些常见的特有名词：
   // A GonicMapper that contains a list of common initialisms taken from golang/lint
   var LintGonicMapper = GonicMapper{
       "API":   true,
       "ASCII": true,
       "CPU":   true,
       "CSS":   true,
       "DNS":   true,
       "EOF":   true,
       "GUID":  true,
       "HTML":  true,
       "HTTP":  true,
       "HTTPS": true,
       "ID":    true,
       "IP":    true,
       "JSON":  true,
       "LHS":   true,
       "QPS":   true,
       "RAM":   true,
       "RHS":   true,
       "RPC":   true,
       "SLA":   true,
       "SMTP":  true,
       "SSH":   true,
       "TLS":   true,
       "TTL":   true,
       "UI":    true,
       "UID":   true,
       "UUID":  true,
       "URI":   true,
       "URL":   true,
       "UTF8":  true,
       "VM":    true,
       "XML":   true,
       "XSRF":  true,
       "XSS":   true,
   }
   1
   2
   3
   4
   5
   6
   7
   8
   9
   10
   11
   12
   13
   14
   15
   16
   17
   18
   19
   20
   21
   22
   23
   24
   25
   26
   27
   28
   29
   30
   31
   32
   33
   34
   35
   36
   37
   函数命名规则
   驼峰式命名，名字可以长但是得把功能，必要的参数描述清楚，函数名名应当是动词或动词短语，如postPayment、deletePage、save。并依Javabean标准加上get、set、is前缀。例如：xxx + With + 需要的参数名 + And + 需要的参数名 + …..

   结构体命名规则
   结构体名应该是名词或名词短语，如Custome、WikiPage、Account、AddressParser，WriteDbMgr，ConfMgr，避免使用Manager、Processor、Data、Info等类名，而且类名不应当是动词。
   结构体中属性名大写，如果属性名小写则在数据解析（如json解析,或将结构体作为请求或访问参数）时无法解析
   包名命名规则
   包名应该为小写单词，不要使用下划线或者混合大小写。

   接口命名规则
   单个函数的接口名以”er”作为后缀，如Reader,Writer。接口的实现则去掉“er”。
   type Reader interface {
       Read(p []byte) (n int, err error)
   }
   1
   2
   3
   4
   //两个函数的接口名综合两个函数名
   type WriteFlusher interface {
       Write([]byte) (int, error)
       Flush() error
   }
   1
   2
   3
   4
   5
   //三个以上函数的接口名，抽象这个接口的功能，类似于结构体名
   type Car interface {
       Start([]byte) 
       Stop() error
       Recover()
   }
   1
   2
   3
   4
   5
   6
   注释
   在编码阶段应该同步写好变量、函数、包的注释，最后可以利用godoc导出文档。注释必须是完整的句子，句子的结尾应该用句号作为结尾（英文句号）。注释推荐用英文，可以在写代码过程中锻炼英文的阅读和书写能力。而且用英文不会出现各种编码的问题。
   每个包都应该有一个包注释，一个位于package子句之前的块注释或行注释。包如果有多个go文件，只需要出现在一个go文件中即可。

   // log包实现了常用的log相关的函数
   package log 

   导出函数注释，第一条语句应该为一条概括语句，并且使用被声明的名字作为开头。
   // 求a和b的和,返回sum.
   func Myfunction(a, b int) (sum int){

   }
   1
   2
   3
   4
   5
   6
   7
   8
   常量
   常量均需使用全部大写字母组成，并使用下划线分词：const APP_VER = “1.0”
   如果是枚举类型的常量，需要先创建相应类型：
   type Scheme string
   const (
       HTTP  Scheme = "http"
       HTTPS Scheme = "https"
   )
   1
   2
   3
   4
   5
   如果模块的功能较为复杂、常量名称容易混淆的情况下，为了更好地区分枚举类型，可以使用完整的前缀：
   type PullRequestStatus int
   const (
       PULL_REQUEST_STATUS_CONFLICT PullRequestStatus = iota
       PULL_REQUEST_STATUS_CHECKING
       PULL_REQUEST_STATUS_MERGEABLE
   )
   1
   2
   3
   4
   5
   6
   变量规则举例
   变量命名基本上遵循相应的英文表达或简写,在相对简单的环境（对象数量少、针对性强）中，可以将一些名称由完整单词简写为单个字母，例如：
   – user 可以简写为 u
   – userID 可以简写 uid
   – 若变量类型为 bool 类型，则名称应以 Has, Is, Can 或 Allow 开头：
   var isExist bool
   var hasConflict bool
   var canManage bool
   var allowGitHook bool
   1
   2
   3
   4
   import
   对import的包进行分组管理，用换行符分割，而且标准库作为分组的第一组。如果你的包引入了三种类型的包，标准库包，程序内部包，第三方包，建议采用如下方式进行组织你的包

   import (
       "github.com/bitly/go-simplejson"
       "strconv"
       "container/list"
       "gopkg/utils"
   )
   1
   2
   3
   4
   5
   6
   在项目中不要使用相对路径引入包：import “../utils”

   参数传递
   对于少量数据，不要传递指针
   对于大量数据的struct可以考虑使用指针
   传入参数是map，slice，chan不要传递指针，因为map，slice，chan是引用类型，不需要传递指针的指针
   单元测试
   单元测试文件名命名规范为 example_test.go
   测试用例的函数名称必须以 Test 开头，例如：TestExample
   ————————————————
   版权声明：本文为CSDN博主「elecjun」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
   原文链接：https://blog.csdn.net/elecjun/article/details/81974777

## 数据类型

- bool
- number
  - int8, int16, int32, int64, int
  - uint8, uint16, uint32, uint64, uint
  - float32, float64
  - complex64, complex128
  - byte (uint8的别名)
  - rune(int32的别名)
- string
- 数组与切片
- map
- pointer
- func
- channel

#### bool

#### int8,int16,int32,int64,int

#### uint8, uint16, uint32, uint64, uint

#### float32, float64

#### complex64, complex128

#### byte (uint8的别名)

#### rune(int32的别名)

rune 是 Go 语言的内建类型，它也是 int32 的别称。

在 Go 语言中，rune 表示一个代码点。代码点无论占用多少个字节，都可以用一个 rune 来表示

#### string

Go 语言中的字符串是一个字节切片，内容放在双引号""之间。Go 中的字符串是兼容 Unicode 编码的，并且使用 UTF-8 进行编码。

1. 定义一个字符串

```go
// 定义一个字符串
//var str string
//str = "hello string"
str := "hello string"
fmt.Println(str)
```

2. 遍历字符串

```go
// len(str) 返回字符串中字节的数量
for i := 0; i < len(str); i++ {
  //fmt.Println(str[i]) // 获取字符串的每一个字节
  // 16 进制打印
  //fmt.Printf("%x ", str[i])
  // %c 格式限定符用于打印字符串的字符
  fmt.Printf("%c ", str[i])
}
```

3. 看下面的代码

```go
str = "Señor"
for i := 0; i < len(str); i++ {
  fmt.Printf("%c ", str[i])
}
// 输出 S e Ã ± o r ，我们循环打印的的由于 ñ 字符占用多个字节，所以没有正确打印正确
```

```go
// 在 Go 语言中，rune 表示一个代码点。代码点无论占用多少个字节，都可以用一个 rune 来表示
runes := []rune(str)
for i := 0; i < len(runes); i++ {
	fmt.Printf("%c ", runes[i])
}
// 输出 S e ñ o r 
```

4. 使用 `for range` 遍历字符串

```go
for i,v := range str {
	// i 返回的是是当前 rune 的字节位置
	fmt.Printf("%v %c \n", i, v)
}
// 输出
0 S 
1 e 
2 ñ 
4 o 
5 r 
从输出中可以看出 ñ 占了2个字节
```

5. 用字节切片构造字符串

```go
bytes := []byte{'a','b','c','d'}
fmt.Println(string(bytes))
```

6. 用 rune 切片构造字符串

```go
runeSlice := []rune{'1', '2', '3', '4', '5'}
fmt.Println(string(runeSlice))
```

7. 字符串的长度

```go
strLen := "cafe baby"
count := utf8.RuneCountInString(strLen)
fmt.Println(count)
```

8. 字符串是不可变的，不能对其修改

```go
str := "hello string"
str[0] = 'l' // 编译报错 Cannot assign to str[0]
```

可以将 字符串转化为 rune 切片，在转化成字符串

```go
str := "hello string"	
i := []rune(str) // 转化为 rune 
i[0] = 'l'
s := string(runeSlice)
fmt.Println(s)
```

#### pointer(指针)

指针是一种存储变量内存地址（Memory Address）的变量。

1. 指针的定义

```go
num := 10
var addr *int // 指针定义
addr = &num// & 取变量 num 的地址值
fmt.Println(addr) // 0x14000022088
// 取指针 addr 的地址
fmt.Println(&addr) // 0x1400000e028
// 取地址值
fmt.Println(*addr) // 10
```

2. Go 不支持指针运算

### 数组

#### 数组定义

```go
// 定一个长度为 3 的 int 类型数组
var arr [3]int
arr = [3]int{}
// 类型推断的方式
arr := [3]int{}
// 部分赋值，全部赋值
arr := [3]int{1,2}
arr := [3]int{1,2,3}
// c长度可以省略
arr := []int{1,2,3}
arr := [...]int{1,2,3}
```

#### 赋值

```go
arr := [3]int{}
arr[0] = 1
arr[1] = 2
arr[2] = 3
// 修改
arr[1] = 4
```

#### 获取长度

```go
arr := [3]int{1,2,3}
length := len(arr)
```

#### 遍历

```go
arr := []int{1,2,3,4,5,6,7,8,9,0}
// for
for i := 0: i < len(arr): i++ {
  fmt.Printf("下标为：%v,值为：%v\n", i, arr[i])
}
// for range
for i,v := range arr {
  fmt.Printf("下标为：%v,值为：%v\n", i, v)
}
```

#### 数组类型

Go 中的数组是值类型而不是引用类型，如果对新变量进行更改，则不会影响原始数组。

```go
arr := []int{1,2,3,4,5}
arr2 := arr;
arr2[0] = 100;
// 数组 arr2 下表为 0 的值变为 100，arr 不变
```

### 切片

## 映射(Map)

### 创建 map

```go
m := make(map[string]int) 
m := map[string]int{}
m := map[string]int{
	"age": 18, // ,不能省略
}
```

### 增

```go
m := map[string]int{}
m["name"] = 1
m["age"] = 2
```

### 改

```go
m := map[string]int{}
m["name"] = 1
m["name"] = 2 // 将 name 改成 2
```

### 删除

使用内置函数 delete 删除

```go
m := map[string]int{}
m["name"] = 1
m["age"] = 23
delete(m, "name") // 删除可以为 name 的映射
```

### 查

```go
m := map[string]int{}
m["name"] = 1
m["age"] = 23

fmt.Println(m["name2"]) // 如果我们获取一个不存在的元素，会返回 int 类型的默认值 0。
val,ok := m["name"] // 获取 name 的值
if ok {
		println(val)
} else {
		println("not exist!")
}
```

### 获取长度

使用内置函数 `len` 获取 map 的长度

```go
m := map[string]int{}
m["name"] = 1
m["age"] = 23
length := len(m) // 获取长度
```

### 遍历

使用 `for range` 遍历 map

```go
m := map[string]int{}
m["name"] = 1
m["age"] = 23
for k,v := range m {
  println(k)
  println(v)
}
```



## 流程控制

### if

1. if

```go
package main
import "fmt"
func main() {
	flag := true
	if flag {
		fmt.Println(flag)
	}
}
```

2. If else

```go
package main
import "fmt"
func main() {
	if flag {
		fmt.Println(flag)
	} else {
		fmt.Println(flag)
	}
}
```

3. if else if

```go
package main
import "fmt"
func main() {
	if flag {
		fmt.Println(flag)
	} else if flag == true {
		fmt.Println(flag)
	} else if flag == false {
		fmt.Println(flag)
	}
}

```

4. 在条件语句中定义变量

```go
package main
import "fmt"
func main() {
	if flag := true； flag {
		fmt.Println(flag)
	}
}
```

### switch

1. switch 分支

```go
switch result {
	case 1:
		println(1)
	case 2:
		println(2)
	//case 2: // 条件不能一样
	case 3:
		println(3)
	default:
		println("default")
	}
```

2. fallthrough

​		如果 case 带有 fallthrough，程序会继续执行下一条 case，且不会判断下一个 case 的表达式是否为 true。

```go
	num := 2
	switch { // 表达式被省略了
	case num >= 0 && num < 10:
		fmt.Println("num is greater than 0 and less than 10")
		fallthrough 
	case num >= 10 && num < 20:
		fmt.Println("num is greater than 10 and less than 10")
		fallthrough
	case num >= 20:
		fmt.Println("num is greater than 20")
	}
```

### for

普通 for 循环

```go
package main
import "fmt"
func main() {
  // 普通 for 循环
	arr := []int{1,2,3,4,5,6,7,8,9,0}
	for i := 0; i < len(arr); i++ {
		fmt.Println(arr[i])
	}
}
```

### for range

 使用 for range 语法遍历数组

```go
package main
import "fmt"
func main() {
	arr := []int{1,2,3,4,5,6,7,8,9,0}
	// for range
	for i,v := range arr {
		fmt.Printf("下标为：%v,值为：%v\n", i, v)
	}
}
```

## 函数

### 函数的定义

语法：

```go
func 函数名() 返回值类型 {
	return 返回值
}
```

### 没有参数的函数

```go
// 0 个参数的函数
func f()  { 
   fmt.Println("0 个参数的函数")
}
```

### 匿名函数

### 闭包

### 字符串函数

### 时间函数

#### now

获取当前时间以及年月日时分秒

```go
package main
import (
	"fmt"
	"time"
)
func main() {
  // 获取当前时间
	now := time.Now()
	fmt.Println(now.Year()) // 年
	fmt.Println(now.Month()) // 月
  fmt.Println(int(now.Month())) // 月（int)
	fmt.Println(now.Day()) // 日
	fmt.Println(now.Hour()) // 时
	fmt.Println(now.Minute()) // 分钟
	fmt.Println(now.Second()) // 秒
}
```

## 内置函数

### len

### delete

### make

## 包 (package)



## 结构体

结构体是用户定义的类型，表示若干个字段（Field）的集合

### 结构体定义

```
type Person(
	age int
)
```

### 方法

```go
type integer int
func (i interger) add(){

}
```

## 文件读写

## io

## 并发

## 网络

## 反射

## db

