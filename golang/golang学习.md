---
typora-copy-images-to: ..\screenshot
---

## 1.环境

### GOPROXY

当我们要拉取依赖，第三方库，默认从配的这个地址去找，如果是direct就是说如果要github的库，就直接从github上拉取，但是这里direct做的事情，难道需要提供一个可下载的链接？

### GO111MODULE

要设置成on，具体还不清楚。。好像是开启go mod，不用依赖管理的gopath和vendor



goimports包可以帮助写代码时的包引入，排序，以及非必须空格的去除

## 2.基础语法

### 定义变量

```go
// 默认值
func main() {
	var a int
	var s string
	// a和s会有默认值
	fmt.Printf("%d, %q", a, s) //0, "" %q会带上""
}

// 赋予初始值
func main() {
	var a int = 1
	var b, c int = 3, 4
	var s string = "abc"
	fmt.Println(a, b, c, s) // 1 3 4 abc
}

// go可推断类型 可以简写
func main() {
	var a, b = 3, 4
	var s = "abc"

	// 甚至可以不同类型写在一起
	var c, d, e = 3, true, "def"
	fmt.Println(a, b, s) // 3 4 abc
	fmt.Println(c, d, e) // 3 true def
}

// 在函数内定义可以省略var
func main() {
	a, b := 3, 4
	s := "abc"
	
	c, d, e := 3, true, "def"
	d = false            // 第二次不能用:
	fmt.Println(a, b, s) // 3 4 abc
	fmt.Println(c, d, e) // 3 true def
}


// 但在函数外定义（即包内变量）则必须用var 不能直接a := 3
var a = 3
var (
	b = 4
	s = "abc"
)
func main() {
	fmt.Println(a, b, s) // 3 4 abc
}
```



### 内建变量

```go
func main() {
	// 布尔
	var flag bool

	// 整型
	var a int // 根据操作系统位数决定
	var b int8
	var c int16
	var d int32
	var e int64
	var aa uint // 无符号
	var bb uint8
	var cc uint16
	var dd uint32
	var ee uint64

	// 指针
	var p uintptr

	// 字节和char
	var zj byte   // 1字节
	var char rune // 4字节 相当于int32的别名

	// 浮点数 和 复数
	var f float32
	var ff float64
	var comp complex64 // 实部和虚部都是32位
	var comp2 complex128
}
```



强转类型

```go
func main() {
	var a, b int = 3, 4
	var c int
	// sqrt需要float64参数 然后c需要int参数
	c = int(math.Sqrt(float64(a*a + b*b)))
	fmt.Println(c) // 5
	// 类型转换是强制的 不能隐式转换
}
```





### 常量

```go
const filename = "123"

const (
	f = 1
	g = 2
)

func main() {
	const a, b = 3, 4 // int可以省略
	const c int = 5
	var d int
	d = int(math.Sqrt(a*a + b*b))     // 如果没有定义a和b的类型 sqrt里就不需要强转了
    
	fmt.Println(filename, a, b, c, d) // 123 3 4 5 5
	fmt.Println(f, g) // 1 2
}
```

枚举

```go
func main() {
	const (
		cpp = iota
		java
		python
		golang
	)
	fmt.Println(cpp, java, python, golang) // 0 1 2 3

	const (
		b = 1 << (10 * iota)
		kb
		mb
		gb
		tb
		pb
	)
	fmt.Println(b, kb, mb, gb, tb, pb) // 1 1024 1048576 1073741824 1099511627776 1125899906842624
}

```



### 条件语句

```go
func f(a int) int {
	if a < 0 {
		return 1
	} else if a > 0 && a < 10 {
		return 2
	} else {
		return 3
	}
}

func main() {
	println(f(10))
}

```

swtich

switch会自动break，除非使用fallthrough

```go
func eval(a, b int, op string) int {
	var result int
	switch op {
	case "+":
		result = a + b
	case "-":
		result = a - b
	case "*":
		result = a * b
	case "/":
		result = a / b
	default:
		panic("unsupported operation")
	}
	return result
}

func main() {
	println(eval(1, 2, "+")) // 3
}
```

swtich也可以没有表达式

```go
func grade(score int) string {
	switch {
	case score < 0 || score > 100:
		panic("Invalid parameters")
	case score < 60:
		return "F"
	case score < 80:
		return "C"
	case score < 90:
		return "B"
	default:
		return "A"
	}
}

func main() {
	println(
		grade(59),
		grade(69),
		grade(89),
	) // F C B
	println(
		grade(101),
	) // panic: Invalid parameters
}
```



### 循环

for的条件里不需要括号

for条件可以省略初始条件，结束条件，递增表达式，所以go里没有while，可以用for代替while

```go
func main() {
	sum := 0
	for i := 1; i <= 10; i++ {
		sum += i
	}
	fmt.Println(sum) // 55
    
    // 死循环
    for {
		fmt.Println("Hello World")
	}
}
```

### 函数

返回值类型写在最后面

可以有多个返回值

函数可以作为参数

没有默认参数，没有可选参数

```go
func f(a int) (b int, c int) {
	return a + 1, a + 2
}
```

函数作为参数例子：

```go
func apply(op func(int, int) int, a, b int) int {
    
    p := reflect.ValueOf(op).Pointer()
	opName := runtime.FuncForPC(p).Name()
	fmt.Println(opName) // 可以打出函数名
    
	return op(a, b)
}

func main() {
	fmt.Println(apply(
		func(a, b int) int {
			return a + b
		}, 1, 2)) // 3
}
```

参数列表是可变的

```go
func f(numbers ...int) int {
	sum := 0
	for _, n := range numbers {
		sum += n
	}
	return sum
}

func main() {
	fmt.Println(f(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)) // 55
}
```



### 指针

go语言只有值传递，就算指针是个形参，也是把指针拷贝一份到参数里去，不过指向的地址还是一样

```go
func f(a *int) int {
    return 2 + *a
}

func main() {
    var a int = 2
    var pa *int = &a
    *pa = 3
    fmt.Println(a) // 3
    fmt.Println(f(&a)) // 5
}
```



## 3.内建容器

### 数组

```go
// 数组需要指定大小 [5]int和[10]int是不同类型
func f(a *[5]int) {
	fmt.Println(*a)
}

func main() {
	var a1 [5]int                 // 声明 会给默认值
	a2 := [3]int{1, 2, 3}         // 需要自己初始化值
	a3 := [...]int{1, 2, 3, 4, 5} // 让go自己算

	var a5 [2][5]bool // 二维数组也有默认值
	fmt.Println(a1, a2, a3, a5)
	// [0 0 0 0 0] [1 2 3] [1 2 3 4 5]
	// [[false false false false false] [false false false false false]]

	// 遍历数组
	for i := 0; i < len(a3); i++ {
		fmt.Println(a3[i]) // 1 2 3 4 5
	}

	for i, v := range a3 {
		fmt.Println(i, v)
		// 0 1
		// 1 2
		// 2 3
		// 3 4
		// 4 5
	}

	// 传参需要传指针 可以避免数组拷贝
	f(&a3)
}
```



### 切片

slice本身没有数据，是对底层数组结构的一个view

```go
slice的结构 {
    ptr // 切片的头节点
    len // 切片长度
    cap // 记录了从切片头节点 到 原数据结构最后一个元素的长度
    // 所以slice可以向后拓展 不可以向前拓展
}

func main() {
	a := [...]int{0, 1, 2, 3, 4, 5, 6, 7}
	s1 := a[2:5]
	fmt.Printf("s1=%v, len(s1)=%d, cap(s1)=%d\n", s1, len(s1), cap(s1)) // s1=[2 3 4], len(s1)=3, cap(s1)=6
}

```

用法

```go
// 入参是一个slice
func f1(s []int) {
    fmt.Println(s)
}

func f2(s []int) {
    s[0] = 100
}

func main() {
    a := [...]int{0, 1, 2, 3, 4, 5, 6, 7, 8}
    s := a[2:6]
    fmt.Println(s) // [2 3 4 5] 左闭右开

    b := [3]int{1, 2, 3}
    // 传参是slice
    f1(a[:]) // [0 1 2 3 4 5 6 7 8]
    f1(b[:]) // [1 2 3]

    // slice是一种view 所以还是会修改原数组
    f2(a[2:])
    f2(a[:])
    fmt.Println(a) // [100 1 100 3 4 5 6 7 8]

    c := [...]int{0, 1, 2, 3, 4, 5, 6, 7, 8}
    s1 := c[2:6]
    s2 := s1[3:5]
    fmt.Println(s1)    // [2 3 4 5]
    fmt.Println(s2)    // [5 6] 向后拓展是允许的
    fmt.Println(s2[2]) // panic: runtime error: index out of range [2] with length 2
    // 因为索引取值会判断slice里的len
}
```

slice添加元素

```go
func main() {
	// 添加元素时如果超过cap 系统会重新分配更大的数组 所以s3和s4是view其他数组 不再view数组s
	s := [...]int{0, 1, 2, 3, 4, 5, 6}
	s1 := s[2:6]
	s2 := append(s1, 100)
	s3 := append(s2, 101)
	s4 := append(s3, 102)
	fmt.Println(s1, s2, s3, s4) // [2 3 4 5] [2 3 4 5 100] [2 3 4 5 100 101] [2 3 4 5 100 101 102]
	fmt.Println(s)              // [0 1 2 3 4 5 100]
}
```

创建、删除slice

```go

func printSlice(s []int) {
	fmt.Printf("len = %d, cap = %d\n", len(s), cap(s))
}

func main() {
	var s []int

	fmt.Println(s) // []

	if s == nil {
		fmt.Println("s is nil") // s is nil
	}

	// 虽然是nil 但往里面添加元素也不会崩溃

	for i := 0; i < 10; i++ {
		printSlice(s)
		// len = 0, cap = 0
		// len = 1, cap = 1
		// len = 2, cap = 2
		// len = 3, cap = 4
		// len = 4, cap = 4
		// len = 5, cap = 8
		// len = 6, cap = 8
		// len = 7, cap = 8
		// len = 8, cap = 8
		// len = 9, cap = 16
		s = append(s, i)
	}
	fmt.Println(s) // [0 1 2 3 4 5 6 7 8 9]

	s1 := []int{1, 2, 3}
	printSlice(s1)
	// len = 3, cap = 3

	// s2初始化都是0
	s2 := make([]int, 10) // len
	printSlice(s2)
	// len = 10, cap = 10

	// s3初始化都是0
	s3 := make([]int, 5, 6) // len cap
	printSlice(s3)
	// len = 5, cap = 6

	// 拷贝
	copy(s2, s1)    // s1数据->s2
	fmt.Println(s2) // [1 2 3 0 0 0 0 0 0 0]

	// 删除
	// 为什么要... 因为append是添加单个元素 不能直接写slice 多个元素的话就写多个 所以要用...
	s2 = append(s2[:2], s2[3:]...) // 相当于 append(s2[:2], 0, 0, 0,...........)
	fmt.Println(s2)                // [1 2 0 0 0 0 0 0 0]
	printSlice(s2)                 // len = 9, cap = 10

	s2 = s2[1:]         // 从前面删除 会导致len和cap都减小
	printSlice(s2)      // len = 8, cap = 9
	s2 = s2[:len(s2)-1] // 但从后面删除不会导致cap减小 因为可以向后拓展
	printSlice(s2)      // len = 7, cap = 9
}

```



### map

除了slice，map，function的内建类型都可以作为key

struct类型不包含上述字段，也可以作为key

```go
func main() {
	// 创建
	m1 := map[string]int{"one": 1, "two": 2, "three": 3}
	m2 := make(map[string]int) // not nil
	var m3 map[string]int      // m3 is nil
	// go中nil也可以参与运算 和 空map可以混用
	fmt.Println(m1, m2, m3) // map[one:1 three:3 two:2] map[] map[]

	// 获取值
	for k, v := range m1 {
		fmt.Println(k, v)
		// two 2
		// three 3
		// one 1
	}

	one := m1["one"]
	fmt.Println(one) // 1

	// 没有则默认获取zero value 即int默认值
	ttwo := m1["ttwo"]
	fmt.Println(ttwo) // 0

	// 如果要判断值是否存在
	ttwo, ok := m1["ttwo"]
	fmt.Println(ok) // false

	// 删除
	three, ok := m1["three"]
	fmt.Println(three, ok) // 3 true
	delete(m1, "three")
	three, ok = m1["three"]
	fmt.Println(three, ok) // 0 false
}

```



### 字符串

```go
func main() {
	s := "Yes我爱慕课网!"
	fmt.Println("len of s:", len(s)) // 19

	for _, b := range []byte(s) {
		fmt.Printf("%X ", b)
		// 59 65 73 E6 88 91 E7 88 B1 E6 85 95 E8 AF BE E7 BD 91 21
		// 一共19个字节 中文占3个字节 这里是utf8的编码
	}
	fmt.Println()

	for i, ch := range s { // ch is rune
		fmt.Printf("(%d %X) ", i, ch)
		// (0 59) (1 65) (2 73) (3 6211) (6 7231) (9 6155) (12 8BFE) (15 7F51) (18 21)
		// 这里是unicode编码
	}
	fmt.Println()

	fmt.Println("rune count:", utf8.RuneCountInString(s)) // 9

	bytes := []byte(s)
	for len(bytes) > 0 {
		ch, size := utf8.DecodeRune(bytes)
		bytes = bytes[size:]
		fmt.Printf("%c ", ch) // Y e s 我 爱 慕 课 网 !
	}
	fmt.Println()

	for i, ch := range []rune(s) {
		fmt.Printf("(%d %c) ", i, ch)
		// (0 Y) (1 e) (2 s) (3 我) (4 爱) (5 慕) (6 课) (7 网) (8 !)
	}
	fmt.Println()
}

```



## 4.面向对象

### 结构体和方法

go语言仅支持封装，不支持继承和多态

```go
type treeNode struct {
    value       int
    left, right *treeNode
}

// 命名方法接收者(node *treeNode) 即是为该结构定义方法

// 值接收者
// 拷贝一份treeNode 不会修改原来的treeNode
func (node treeNode) setValue1(value int) {
    node.value = value
}

// 指针接收者
// 会修改原来的treeNode
func (node *treeNode) setValue2(value int) {
    node.value = value
}

func main() {
    var root treeNode
    fmt.Println(root) // {0 <nil> <nil>}
    root = treeNode{value: 3}
    root.left = &treeNode{}
    root.right = &treeNode{5, nil, nil}
    root.right.left = new(treeNode) // new 内建函数
    fmt.Println(*root.right.left)   // {0 <nil> <nil>}

    // slice
    nodes := []treeNode{
       {value: 3},
       {},
       {6, nil, &root},
    }
    fmt.Println(nodes) // [{3 <nil> <nil>} {0 <nil> <nil>} {6 <nil> 0xc000008048}]

    root.setValue1(4)
    fmt.Println(root) // {3 0xc000008078 0xc000008090}
    root.setValue2(4)
    fmt.Println(root) // {4 0xc000008078 0xc000008090}
}
```



### 包和封装

名字一般使用CamelCase

首字母大写：public

首字母小写：private



每个目录一个包

main包包含了可执行入口

为结构定义的方法必须放在同一个包内，但可以是不同文件

## 5.依赖管理

依赖管理的3个阶段：**gopath、govendor、go mod**



假设goroot设置成/usr/local/go

gopath设置成/private/tmp/gopathtest



### **gopath**

默认在~/go，~/go下必须有个文件夹src

无论什么依赖都在gopath下找

缺点：所有自己写的项目，以及依赖第三方都在gopath/src下，并且如果两个项目要依赖log包的不同版本，这是没有办法的做管理的

```go
如果import github.com/aaa/bbb
那么会报错cannot find package.....
/usr/local/go/src/github.com/aaa/bbb(from goroot)
/private/tmp/gopathtest/src/github.com/aaa/bbb(from gopath)
所以会先从goroot的src下找，再从gopath的src下找
```



### **govendor**

那么为了解决不同项目依赖不同包的版本问题，可以在自己项目的根目录创建一个vendor目录管理三方包，例如：

```go
你的project在/private/tmp/gopathtest/src/project123
你需要创建vendor,即
/private/tmp/gopathtest/src/project123/vendor
然后将需要的依赖包放入到这个目录下
当找package的时候
会先从vendor中找，再从goroot，gopath下找
```

但是如果手工拷贝依赖包到vendor目录无疑很麻烦，所以有很多第三方依赖管理工具去做这个事情：

glide，dep，go dep等，可以通过类似maven的xml配置方式，把依赖拉取下来，再拷贝到vendor下



### **go mod**

类似maven，由go命令统一管理三方包，用户不需要在每个项目都维护vendor

```go
比如你要import github.com/aaa/bbb的version14
那么你go get后
在gopath下就会有pkg/mod/github.com/aaa/bbb@v14.0.0
```



### **升级项目到go mod模式**

变量go111module等要设置好

go mod init project123

通过go build ./...命令，即把当前目录下的所有子目录和go有关的文件都build下



每个目录下都可以有一个main函数，且这个函数需要在package main中



## 6.面向接口

实现者不需要说明实现了哪一个接口，只需要实现了接口里的方法就可以

```go
import (
	"fmt"
	"net/http"
	"net/http/httputil"
	"time"
)

const url = "http://www.baidu.com"

// get接口
type Retriever interface {
	Get(url string) string
}

func download(r Retriever) string {
	return r.Get(url)
}

// post接口
type Poster interface {
	Post(url string, form map[string]string) string
}

func post(poster Poster) {
	poster.Post(url, map[string]string{
		"name":   "ccmouse",
		"course": "golang",
	})
}

// get和post接口
type RetrieverPoster interface {
	Retriever
	Poster
}

func session(s RetrieverPoster) string {
	s.Post(url, map[string]string{
		"contents": "another contents",
	})

	return s.Get(url)
}

// struct1 实现了get和post方法
type Struct1 struct {
	Contents string
}

func (r *Struct1) Get(url string) string {
	return r.Contents
}

func (r *Struct1) Post(url string, form map[string]string) string {
	r.Contents = form["contents"]
	return r.Contents
}

// struct1 end

// struct2 start
type Struct2 struct {
	UserAgent string
	TimeOut   time.Duration
}

func (r Struct2) Get(url string) string {
	resp, err := http.Get(url)
	if err != nil {
		panic(err)
	}

	result, err := httputil.DumpResponse(resp, true)
	resp.Body.Close()

	return string(result)
}

// struct2 end

func main() {
	var r Retriever
	rStruct1 := Struct1{"this is a content retriever"}
	r = &rStruct1
	fmt.Println(download(r))        // this is a content retriever
	fmt.Println(session(&rStruct1)) // another contents
	fmt.Printf("%T %v\n", r, r)     // *main.RetrieverStruct1 &{another contents}
	fmt.Println(".............................")
	r = Struct2{
		UserAgent: "Mozilla/5.0",
		TimeOut:   time.Minute,
	}
	fmt.Println(download(r))    // html文本
	fmt.Printf("%T %v\n", r, r) // main.RetrieverStruct2 {Mozilla/5.0 1m0s}
}

```



## 7.函数式编程

写法

```go
// 写法1
func adder1() func(int) int {
	sum := 0
	return func(v int) int {
		sum += v
		return sum
	}
}

// 写法2
type iAdder func(int) (int, iAdder)

func adder2(base int) iAdder {
	return func(v int) (int, iAdder) {
		return base + v, adder2(base + v)
	}
}

func main() {
	a := adder1()
	for i := 0; i < 10; i++ {
		fmt.Println(a(i))
        // 0 1 3 6 10 15 21 28 36 45
	}

	b := adder2(0)
	for i := 0; i < 10; i++ {
		var s int
		s, b = b(i)
		fmt.Println(s)
        // 0 1 3 6 10 15 21 28 36 45
	}
}

```





## 8.错误处理和资源

### defer调用

确保调用在函数结束时发生

defer列表为后进先出



### 何时用defer

open/close

lock/unlock

```go
func main() {
	// defer类似栈 先进后出
	defer fmt.Println(1)
	defer fmt.Println(2)
	fmt.Println(3)
	panic("error appear")
	fmt.Println(4)
}
// 3
// 2
// 1
// panic: error appear
```



错误处理

```go
func main() {
	// panic会把程序挂掉

	// 如果出现error 一般不进行panic 我们应该做的是打印日志 + return, 因为进行不下去了

	// 获取错误类型
	file, err := os.Open("test.txt")
	if err != nil {
		// 可以通过 .()获取里面具体错误类型, 因为error是interface实现 只要有Error方法都是可以点出来的
		if pathErr, ok := err.(*os.PathError); ok {
			fmt.Println("pathErr: ", pathErr.Err)
		} else {
			fmt.Println("unknown error type: ", err)
		}
	}

	defer file.Close()
}
```



### 服务器统一出错处理

```go
package main

import (
	"io/ioutil"
	"net/http"
	"os"
)

type addHandler func(http.ResponseWriter, *http.Request) error

// 这里用wrapper统一把 内部流转错误 转为 外部通用错误
func errWrapper(handler addHandler) func(w http.ResponseWriter, r *http.Request) {
	return func(w http.ResponseWriter, r *http.Request) {
		err := handler(w, r)
		if err != nil {
			code := http.StatusOK
			switch {
			case os.IsNotExist(err):
				code = http.StatusNotFound
			case os.IsPermission(err):
				code = http.StatusForbidden
			default:
				code = http.StatusInternalServerError
			}
			http.Error(w, http.StatusText(code), code)
		}

	}
}

func handleFileList(writer http.ResponseWriter, request *http.Request) error {
	path := request.URL.Path[len("/list/"):]
	file, err := os.Open(path)
	if err != nil {
		return err
	}
	defer file.Close()

	all, err := ioutil.ReadAll(file)
	if err != nil {
		return err
	}

	writer.Write(all)
	return nil
}

func main() {
	http.HandleFunc("/list/", errWrapper(handleFileList))

	err := http.ListenAndServe(":8080", nil)
	if err != nil {
		panic(err)
	}
}

```



### panic和recover

panic的作用：

停止当前函数的执行

一直向上返回，执行每一层的defer

如果没有遇到recover，程序退出



recover：

仅在defer调用中使用

获取panic的值

如果无法处理，可重新panic



recover例子：

```go
func tryRecover() {
	// panic和recover都是interface{}
	defer func() {
		r := recover()
		if err, ok := r.(error); ok {
			fmt.Println("error occurred", err)
		}
	}()

	panic(errors.New("abc error"))
	fmt.Println("123") // 不会执行
}

func main() {
	tryRecover()
	fmt.Println("wow") // 会执行
}

```



## 9.测试与性能调优

### 传统测试vs表格驱动测试

传统测试：

测试数据和测试逻辑混在一起

一个数据出错全部测试结束

```java
@Test public void testAdd() {
	assertEquals(3, add(1, 2));
	assertEquals(2, add(0, 2));
	assertEquals(0, add(0, 0);
	assertEquals(Integer.MIN_VALUE, add(1, Integer.MAX_VALUE));
}
```



表格驱动测试：

分离测试数据和测试逻辑

可以部分失败



文件名需要*_test.go



### 单元测试

go test . 测试当前目录下所有测试函数?

go test-coverprofile=c.out就能把结果输出到c.out，然后通过 go tool cover -html=c.out就能显示

```go
// add_test.go
package main

import "testing"

func add(x int, y int) int {
	return x + y
}

func TestAdd(t *testing.T) {
	tests := []struct {
		a, b, c int
	}{
		{1, 2, 3},
		{5, 7, 12},
		{7, 6, 13},
		{1, 200, 201},
		{3, 3, 7},
		{33, 97, 130},
	}

	for _, test := range tests {
		if actual := add(test.a, test.b); actual != test.c {
			t.Errorf("Add(%d, %d) got %d, expected %d", test.a, test.b, actual, test.c) // Add(3, 3) got 6, expected 7
		}
	}
}

```



### 性能测试

如果使用go test -bench .即调用压测函数

```go

import "testing"

func add(x int, y int) int {
	return x + y
}

func BenchmarkAdd(b *testing.B) {
	one := 2020202023
	two := 31232214511
	three := 33252416534
    // b.N系统会给予一个值（具体原理可自查）
	for i := 0; i < b.N; i++ {
		if actual := add(one, two); actual != three {
			b.Errorf("Add(%d, %d) got %d, expected %d", one, two, actual, three)
		}
	}
}

// 结果代表运行了1000000000次, 每次运行了0.2525ns
// goos: windows
// goarch: amd64
// pkg: firstProject
// cpu: Intel(R) Core(TM) i5-10400F CPU @ 2.90GHz
// BenchmarkAdd
// BenchmarkAdd-12    	1000000000	         0.2525 ns/op
// PASS

```



### 使用pprof优化性能

使用go test -bench . -cpuprofile cpu.out，然后用go tool pprof cpu.out，进行交互式，然后输入web回车，就能看到性能图，接下来就是看哪个部分慢，可以做优化的（需要先下载graphviz）



### 生成文档和示例代码

具体可看go doc，godoc命令



## 10.goroutine

### 协程coroutine

轻量级“线程”

非抢占式多任务处理，由协程主动交出控制权

多个协程可能在一个或多个线程上运行



简单示例：

```go
func main() {
	for i := 0; i < 10; i++ {
		go func(i int) {
			for {
				fmt.Println(i)
			}
		}(i)
	}

	time.Sleep(time.Millisecond)
}

```



**以下自测没有出现退不出来的情况，有时间可以问问go大佬**

但如果改成这样，就会出现main函数结束不了的情况。本质是因为上面的程序有fmt.println，即有io，所以协程之间能进行切换（执行main函数的本质上也是个协程），但是如果只是做数组改值，则不涉及到切换，sleep后没有切换回main执行，所以退出不了。

```go
func main() {
	var a [10]int
	for i := 0; i < 10; i++ {
		go func(i int) {
			for {
				a[i]++
                // runtime.Gosched()
			}
		}(i)
	}

	time.Sleep(time.Millisecond)
	fmt.Println(a)
}

```

通过runtime.Gosched()可以主动交出控制权



main函数调用doWork函数，可能这两个函数在同一个线程上，也可能在不同线程上执行，反正就是2个协程，具体看调度器的逻辑，他们两是有一个双向通道可以通信，数据可以双向流通，控制权也可以双向流通。

![image-20240607224017806](F:\git_project\LearningDocuments\screenshot\image-20240607224017806.png)





调度器控制怎么分配协程到线程上

![image-20240607224418774](F:\git_project\LearningDocuments\screenshot\image-20240607224418774.png)



### goroutine定义

任何函数只需加上go就能送给调度器运行

不需要在定义时区分是否是异步函数

调度器在合适的点进行切换

使用-race来检测数据访问冲突



### goroutine可能切换的切换点

io，select

channel

等待锁

函数调用（有时）

runtime.Gosched()

（只是参考，不能保证一定切换）



## 11.channel

goroutine和goroutine之间用channel交互

![image-20240608185739034](F:\markdowm图片保存\image-20240608185739034.png)



看一个死锁的例子：

```go
func chanDemo() {
	// var c chan int // c == nil

	c := make(chan int)
	c <- 1 // // fatal error: all goroutines are asleep - deadlock!
	c <- 2
	n := <-c
	fmt.Println(n)
}

func main() {
	chanDemo()
}
```

因为没有一个goroutine去接收它，所以会deadlock

```go
func chanDemo() {
	// var c chan int // c == nil
    // var channels [10]chan int

	c := make(chan int)

	// 需要有一个goroutine去接收c
	go func() {
		for {
			n := <-c
			fmt.Println(n) // 1 2
		}
	}()

	c <- 1
	c <- 2

	time.Sleep(time.Millisecond)
}

func main() {
	chanDemo()
}

// 当然channel也可能当作参数
// 比如把代码改成:
func worker(c chan int) {
	for {
		n := <-c
		fmt.Println(n)
	}
}
go worker(c)
```



类型为 chan<- int，代表这个channel只能收数据，类型为<-chan int代表只能发数据



如果每次发送一个int到channel，就需要切换协程，让接收者处理，无疑比较消耗资源，可以创建buffer channel，提升性能

```go
func bufferedChannel() {
	c := make(chan int, 3)
	c <- 1
	c <- 2
	c <- 3
	// c <- 4
    // 加到第4个才会deadlock
}

func main() {
	bufferedChannel()
}
```



当channel发送完毕，需要通知接收者关闭

```go
func worker(c chan int) {
	for {
		n, ok := <-c
		if !ok {
			break
		}
		fmt.Println(n)
	}
	// 或者
	//for n := range c {
	//
	//	fmt.Println(n)
	//}
}

func bufferedChannel() {
	c := make(chan int, 3)
	go worker(c)
	c <- 1
	c <- 2
	c <- 3
	c <- 4
	c <- 5
	close(c)
}

func main() {
	bufferedChannel()
	time.Sleep(time.Millisecond)
}

```



channel的实现理论基础是csp，即communication sequential process

不要通过共享内存来通信；通过通信来共享内存

dont communicate by sharing memory; share memory by communicating



通过channel通知任务结束

```go
type worker struct {
	in   chan int
	done chan bool
}

func doWork(id int, in chan int, done chan bool) {
	for n := range in {
		fmt.Printf("Worker:%d received %d\n", id, n)
		done <- true
	}
}

func createWorker(id int) worker {
	w := worker{
		in:   make(chan int),
		done: make(chan bool),
	}

	go doWork(id, w.in, w.done)
	return w
}

func main() {
	var workers [10]worker
	for i := 0; i < 10; i++ {
		workers[i] = createWorker(i)
	}

	for i := 0; i < 10; i++ {
		workers[i].in <- 'a' + i
	}

	// wait for all of them
	for _, w := range workers {
		<-w.done
	}
}

```



但上面的还有更好的做法，使用waitgroup来管理

```go
type worker struct {
	in   chan int
	done func()
}

func doWork(id int, w worker) {
	for n := range w.in {
		fmt.Printf("Worker:%d received %d\n", id, n)
		w.done()
	}
}

func createWorker(id int, wg *sync.WaitGroup) worker {
	w := worker{
		in: make(chan int),
		done: func() {
			wg.Done()
		},
	}

	go doWork(id, w)
	return w
}

func main() {
	var workers [10]worker
	var wg sync.WaitGroup
	wg.Add(10)
	for i := 0; i < 10; i++ {
		workers[i] = createWorker(i, &wg)
	}

	for i := 0; i < 10; i++ {
		workers[i].in <- 'a' + i
	}

	wg.Wait()
}

```



go中的select条件表达只能是通信操作，而switch可以是任意类型

switch语句不会阻塞，但selcet语句会在所有操作都阻塞的情况下阻塞，直到有一个操作可以执行为止



### 传统同步机制

waitgroup

mutex

cond



## 命令

go run hello.go，运行main函数

go mod init xxx，初始化gomod

go mod tidy可以整理go.sum，比如说替换了包的版本，在sum里会同时存在两个版本

go get github.com/a/b@v1.0.0指定版本增加依赖，-u命令就是更新到最新

go build编译代码，go build ./...编译目录下（子目录等）的所有代码

go install产生执行文件（默认放在gopath/bin下）和pkg文件

go test . 测试当前目录下所有测试函数?

go test-coverprofile=c.out就能把结果输出到c.out，然后通过 go tool cover -html=c.out就能显示

如果使用go test -bench .即调用压测函数

使用go test -bench . -cpuprofile cpu.out，然后用go tool pprof cpu.out，进行交互式，然后输入web回车，就能看到性能图，接下来就是看哪个部分可以做优化（需要先下载graphviz）



## 疑惑

为什么go有reflect？



a := []int{1, 2, 3}是数组初始化吗？



以下自测没有出现退不出来的情况，有时间可以问问go大佬

但如果改成这样，就会出现main函数结束不了的情况。本质是因为上面的程序有fmt.println，即有io，所以协程之间能进行切换（执行main函数的本质上也是个协程），但是如果只是做数组改值，则不涉及到切换，sleep后没有切换回main执行，所以退出不了。

```go
func main() {
	var a [10]int
	for i := 0; i < 10; i++ {
		go func(i int) {
			for {
				a[i]++
			}
		}(i)
	}

	time.Sleep(time.Millisecond)
	fmt.Println(a)
}

```

猜测是因为main用了另一个线程的协程去跑，因为go的调度器可以分配哪一个线程中的协程去跑，所以如果我数组开大点，应该main退出不了，因为占用了所有cpu，当我开到10000后，卡住了，但不知是系统卡死了，还是真的是我想的那样。。