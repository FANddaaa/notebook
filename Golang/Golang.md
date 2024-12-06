# Golang 教程
## <a id="contents">目录</a>
**[1 零散语法](#title1)**  
**[2 函数](#title2)**  
[2.1 init 函数](#title2.1)  
[2.2 匿名函数](#title2.2)  
[2.3 闭包](#title2.3)  
[2.4 defer 关键字](#title2.4)  
[2.5 字符串函数](#title2.5)  
[2.6 日期和时间函数](#title2.6)  
[2.7 内置函数](#title2.7)  
**[3 错误处理](#title3)**  
[3.1 defer+recover机制处理错误](#title3.1)  
[3.2 自定义错误](#title3.2)  
**[4 数组](#title4)**  
[4.1 数组的初始化方式](#title4.1)  
[4.2 数组的注意事项](#title4.2)  
**[5 切片](#title5)**  
[5.1 切片的定义](#title5.1)  
[5.2 切片的注意事项](#title5.2)  
**[6 映射](#title6)**  
[6.1 映射的定义](#title6.1)  
[6.2 map的操作](#title6.2)  
**[7 面向对象](#title7)**  
[7.1 方法的引入](#title7.1)  
[7.2 方法的注意事项](#title7.2)  
[7.3 封装的实现](#title7.3)  
[7.4 继承的实现](#title7.4)  
[7.5 接口的实现](#title7.5)  
[7.6 多态](#title7.6)  
[7.7 断言](#title7.7)  
**[8 文件的操作](#title8)**  
[8.1 读取文件](#title8.1)  
[8.2 写入文件](#title8.2)  
**[9 协程和管道](#title9)**  
[9.1 协程概念辨析](#title9.1)  
[9.2 主线程和协程执行流程](#title9.2)  
[9.3 使用WaitGroup控制协程退出](#title9.3)  
[9.4 使用互斥锁同步协程](#title9.4)  
[9.5 读写锁](#title9.5)  
[9.6 管道](#title9.6)  
[9.6.1 管道入门](#title9.6.1)  
[9.6.2 管道的遍历](#title9.6.2)  
[9.6.3 协程和管道协同工作](#title9.6.3)  
[9.6.4 select功能](#title9.6.4)  
**[10 网络编程](#title10)**  
[10.1 创建TCP客户端](#title10.1)  
[10.2 创建TCP服务器](#title10.2)  
**[11 反射](#title11)**  
[11.1 对结构体进行反射](#title11.1)  
[11.2 通过反射操作结构体的属性和方法](#title11.2)  
[11.3 通过反射修改结构体变量的值](#title11.3)  

## <a id="title1">1 零散语法 [📜](#contents)</a>

* `> gofmt test.go // 仅效果展示`

  `> gofmt -w test.go // 更改go源文件`

* import导入包名是从`$GOPATH/src/`后开始计算的。

* Golang不支持函数重载，支持可变参数 (函数内部处理可变参数时将其当作**切片**来处理)

## <a id="title2">2 函数 [📜](#contents)</a>

### <a id="title2.1">2.1 init 函数</a>

init函数：初始化函数，每个源文件都可以包含一个init函数

执行顺序：全局变量 -> init函数 -> main函数 (按照import顺序，最后执行main包)

### <a id="title2.2">2.2 匿名函数</a>

```go
result := func (num1 int, num2 int) int {
    return num1 + num2
} (10, 20)
```

### <a id="title2.3">2.3 闭包</a>

闭包就是一个函数和与其相关的引用环境组合的一个整体

闭包应用场景：可以保留上次引用的某个值，传入一次就可以反复使用了

```go
func main() {
    f := getSum()
    fmt.Println(f(1)) // 1
    fmt.Println(f(2)) // 3
    fmt.Println(f(3)) // 6
}
    
func getsum() fun (int) int {
    var sum int = 0
    return func (num int) int {
        sum += num
        return sum
    }
} // 闭包：返回的匿名函数 + 匿名函数以外的变量
```

### <a id="title2.4">2.4 defer 关键字</a>

Golang中程序遇到defer关键字不会立即执行defer后的语句，而是将defer后的语句压入一个栈中，继续执行函数后面的语句。

应用场景：在使用某个资源时顺手defer关闭该资源

### <a id="title2.5">2.5 字符串函数</a>

```go
// 统计字符串的长度(按字节)
str := "Golang你好"  // 在Golang中 汉字是utf-8字符集(3字节)
fmt.Println(len(str)) // 12

// 对字符串进行遍历
// 方式1: 利用键值循环(for-range)
for i, value := range str {
    fmt.Printf("索引为: %d，具体的值为: %c \n", i, value)
}
// 方式2: 利用切片
r := []rune(str)
for i := 0; i < len(r); i++ {
    fmt.Printf("%c \n", r[i])
}

// 字符串转整数
num1, _ := strconv.Atoi("666")
// 整数转字符串
str1 := strconv.Itoa(88)

// 统计一个字符串有多少个指定的字串
count := strings.Count("golangandjava", "ga") // 1

// 不区分大小写的字符串比较
flag := strings.EqualFold("hello", "HELLO") // true
// 区分大小写进行字符串比较
fmt.Println("hello" == "Hello") // false

// 返回字串在字符串第一次出现的索引值，如果没有返回-1
strings.Index("golangandjava", "ga") // 5

// 字符串的替换(-1表示替换全部)
str2 := strings.Replace("goandjavagogo", "go", "golang", -1)

// 按照指定的某个字符为分割标识，将一个字符串拆分成字符串数组
arr := strings.Split("go-python-java", "-") // [go python java]

// 字符串的字母大小写切换
fmt.Println(strings.ToLower("Go"))
fmt.Println(strings.ToUpper("go"))

// 将字符串左右两边的空格去掉
fmt.Println(strings.TrimSpace("       go and java       "))
// 将字符串左右两边的指定字符去掉
fmt.Println(strings.Trim("~golang~", "~")) // TrimLeft & TrimRight

// 判断字符串是否以指定的字符串开头&结尾
strings.HasPrefix("http://java.sun.com/jsp/jstl/fmt", "http") // true
strings.HasSuffix("demo.png", ".jpg") // false
```

### <a id="title2.6">2.6 日期和时间函数</a>

```go
// 时间和日期的函数，需要导入time包，获取当前时间需要调用Now函数
now := time.Now()
// 该参数字符串的各个数字是固定的
datestr := now.Format("2006-01-02 15:04:05")
```

### <a id="title2.7">2.7 内置函数</a>

为编程方便提供的一些函数，不需要导包也可直接使用。

1. len函数：统计字符串的长度(按字节进行统计)

2. new函数：分配内存，主要用于分配值类型(int系列、float系列、bool、string、数组和结构体struct)

3. make函数：分配内存，主要用来分配引用类型(指针、切片slice、map、管道chan、interface等)

## <a id="title3">3 错误处理 [📜](#contents)</a>

### <a id="title3.1">3.1 defer+recover机制处理错误</a>

```go
func test() {
    // 利用defer+recover来捕获错误：defer后加上匿名函数的调用
    defer func() {
        // 调用recover内置函数捕获错误
        err := recover()
        // 如果没有捕获错误就会返回nil
        if err != nil {
            fmt.Println("err是：", err)
        }
    }()
    num1 := 10
    num2 := 0
    result := num1 / num2
}
```

### <a id="title3.2">3.2 自定义错误</a>

调用errors包下的New函数，函数返回error类型

## <a id="title4">4 数组 [📜](#contents)</a>

### <a id="title4.1">4.1 数组的初始化方式</a>

```go
var arr1 [3]int = [3]int{3,6,9}
var arr2 = [3]int{1,4,7}
var arr3 = [...]int{4,5,6,7}
var arr4 = [...]int{2:66,0:33,1:99,3:88}
```

### <a id="title4.2">4.2 数组的注意事项</a>

1. 长度属于类型的一部分
2. Go中数组属于值类型，因此会进行值拷贝。若要在其他函数中修改原数组，可以使用引用传递(指针方式)

## <a id="title5">5 切片 [📜](#contents)</a>

### <a id="title5.1">5.1 切片的定义</a>

Go中数组长度不可变，切片构建在数组之上，是对数组一个连续片段的引用(切片是引用类型)，切片包含3个字段：指向底层数组的指针，切片的长度、容量

``` go
// 定义数组
var intArr [6]int = [6]int{3, 6, 9, 1, 4, 7}
// 切片构建在数组之上
var slice []int = intArr[1:3] // [6 9]

fmt.Printf("数组中下标为1的地址：%p\n", &intArr[1])
fmt.Printf("切片中下标为0的地址：%p\n", &slice[0])
```

``` go
// make函数的3个参数：切片的类型、长度、容量
// make底层创建一个外部不可见的数组，只能通过slice间接访问各个元素
slice1 := make([]int, 4, 20)
slice2 := []int{1, 4, 7} // 类似make
```

### <a id="title5.2">5.2 切片的注意事项</a>

1. 切片使用不能越界
2. 切片可以继续切片
3. 切片可以动态增长

 ``` go
var intArr [6]int = [6]int{1, 4, 7, 3, 6, 9}
var slice []int = intArr[1:4] // [4 7 3]
slice2 := append(slice, 88, 50) // [4 7 3 88 50]
// 底层原理：
// 1.底层追加元素时对数组进行扩容，老数组扩容为新数组
// 2.创建一个新数组(将4 7 3复制并追加88 50)
// 3.slice2底层指向的是外部不可见的新数组
// 4.可以直接使用slice接收

slice3 := []int{99, 44}
slice = append(slice, slice3...) // ...不可省略！代表追加的是切片
 ```

4. 切片的拷贝

 ``` go
var a []int = []int{1, 4, 7, 3, 6, 9}
var b []int = make([]int, 10)
copy(b, a) // [1 4 7 3 6 9 0 0 0 0]
 ```

## <a id="title6">6 映射 [📜](#contents)</a>

### <a id="title6.1">6.1 映射的定义</a>

映射(map)是Go语言的一种内置类型，将键值相关联(键值对) 

1. 基本语法：`var name map[keyType]valueType`
2. key、value类型：bool/数字/string/指针/channel，也可以是<font color="red">只</font color>包含前面几个类型的接口/结构体/数组
3. key：通常为int/string，不可以是slice/map/function
4. value：通常为数字(整数、浮点数)/string/map/结构体

``` go
// 定义map变量，只声明是没有分配空间，通过make函数初始化才会分配空间
var a map[int]string
// make函数的第2个参数size可以省略(默认为1)
a = make(map[int]string, 10) // 可以存放10个键值对
// map的key-value是无序的
a[1951895] = "张三"
a[1952007] = "李四"

b := make(map[int]string)
c := map[int]string {
    1951895 : "张三",
    1952007 : "李四",
}
```

### <a id="title6.2">6.2 map的操作</a>

``` go
// 删除
delete(a ,1951895)
// 查找
value,flag := a[1952007]
// 遍历(仅支持for-range)
for k,v := range a {}
```

## <a id="title7">7 面向对象 [📜](#contents)</a>

Golang支持面向对象编程(OOP)但并不是纯粹的面向对象语言，我们说Golang**支持面向对象编程特性**是比较精确的；Golang是基于**结构体struct**(没有类class)实现OOP特性；Golang面向对象非常简洁，去掉了传统OOP语言的**方法重载**、**构造函数**、**析构函数**和**隐藏指针this**等等，仍然有OOP的**继承**、**封装**和**多态**的特性，实现方式和其他OOP语言不一样，比如：Golang没有extends关键字，通过匿名字段来实现继承。

### <a id="title7.1">7.1 方法的引入</a>

``` go
type A struct {
    Num int
}
func (a A) test() { // A结构体有一个test方法
    fmt.Println(a.Num)
}
```

### <a id="title7.2">7.2 方法的注意事项</a>

``` go
type Student struct {
    Name string
    Age int
}
func (s *Student) String() string {
    str = fmt.Sprintf("Name = %v, Age = %v", s.Name, s.Age)
    return str
}

func main() {
    stu := Student {
        Name : "丽丽",
        Age : 20,
    }
    // 如果绑定了String方法就会自动调用
    fmt.Println(&stu)
}
```

### <a id="title7.3">7.3 封装的实现</a>

``` go
type person struct {
    Name string
    age int // 其它包不能直接访问
}
// 定义工厂模式的函数(相当于构造器)
func NewPerson(name string) *person {
    return &person {
        Name : name,
    }
}
// 定义set和get方法，对age字段进行封装，确保被封装字段的安全合理性
func (p *person) SetAge(age int) {
    if age > 0 && age < 150 {
        p.age = age
    } else {
        fmt.Println("年龄范围不正确")
    }
}
func (p *person) GetAge() int {
    return p.age
}
```

### <a id="title7.4">7.4 继承的实现</a>

``` go
// 定义结构体：Animal
type Animal struct {
    Age int
    weight float32
}
func (an *Animal) shout() {
    fmt.Println("我可以大声喊叫")
}

// 定义结构体：Cat
type Cat struct {
    // 加入匿名结构体体现继承思想，提高代码复用性
    Animal
}
// Cat特有的方法
func (c *Cat) Scratch() {
    fmt.Println("我可以挠人")
}

func main() {
    cat := &Cat {}
    cat.Animal.Age = 3
    // 即使嵌套匿名结构体的字段和方法首字母小写也能使用
    cat.Animal.weight = 10.6
    // .Animal可以省略
    cat.shout()
    cat.Scratch()
}
```

### <a id="title7.5">7.5 接口的实现</a>

``` go
type SayHello interface {
    // 声明没有实现的方法
    sayHello()
}

// 实现接口要实现所有的方法
type Chinese struct {}
func (person Chinese) sayHello() {
    fmt.Println("你好👋")
}
type American struct {}
func (person American) sayHello() {
    fmt.Println("Hi👋")
}

// 定义一个函数，接受具备SayHello接口能力的变量
func greet(s SayHello) {
    s.sayHello()
}

func main() {
    c := Chinese{}
    a := American{}
    
    greet(c)
    greet(a)
    
    // 接口无法直接创建实例，但可以指向一个实现该接口的自定义类型变量
    var s SayHello = c
    
    // interface类型默认是一个指针(引用类型)，如果没有对interface初始化就使用，会输出nil
    // 空接口没有任何方法，因而可以把任何变量赋给空接口
    type E interface {}
    var e1 E = 1
    var e2 interface{} = 2.0
}
```

### <a id="title7.6">7.6 多态</a>

``` go
// Go语言的多态特征是通过接口实现的
// s叫多态参数
func greet(s SayHello) {
    s.sayHello()
}

func main() {
    // 多态数组
    var arr [3]SayHello
    arr[0] = American{}
    arr[1] = Chinese{}
    arr[2] = Chinese{}
}
```

### <a id="title7.7">7.7 断言</a>

``` go
func greet(s SayHello) [
    s.sayHello()
    // 断言：看s是否能转成Chinese类型并赋给ch变量
    if ch,flag := s.(Chinese); flag {
        ch.niuYangGe()
    }
    
    switch s.(type) { // type是关键字
        case Chinese:
            ch := s.(Chinese)
            ch.niuYangGe()
        case American:
            am := s.(American)
            am.disco()
    }
}
```

## <a id="title8">8 文件的操作 [📜](#contents)</a>

``` go
func main() {
    // 打开文件
    file,err := os.Open("d:/test.txt")
    if err != nil {
        fmt.Println(err)
    }
    // 关闭文件
    err = file.Close()
    if err != nil {
        fmt.Println(err)
    }
}
```

### <a id="title8.1">8.1 读取文件</a>

``` go
func main() {
    // 读取文件：不需要Open/Close，因为被封装在ReadFile函数内部了
    // 一次性读取整个文件，适合文件不大的情况
    if content,err := ioutil.ReadFile("d:/test.txt"); err != nil {
        fmt.Println(err)
    }
    fmt.Printf("%v", string(content))
}
```

**以下代码有误！**

``` go
func main() {
    // 打开文件
    file,err := os.Open("d:/test.txt")
    if err != nil {
        fmt.Println("文件打开失败，err",err)
        return
    }
    defer file.Close()
    // 创建一个流
    read := bufio.NewReader(file)
    // 读取操作
    for {
        str,err := reader.ReadString('\n') // 读取到换行就结束
        if err == io.EOF {
            break
        }
        fmt.Println(str)
    }
}
```

### <a id="title8.2">8.2 写入文件</a>

``` go
func main() {
    file,err := os.OpenFile("d:/test.txt",os.O_RDWR | os.O_APPEND | os.O_CREATE,0666)
    fmt.Println(os.FileMode(0666).String())
    if err != nil {
        fmt.Println("文件打开失败，",err)
        return
    }
    defer file.Close()
    writer := bufio.NewWriter(file)
    for i :=; i < 10; i++ {
        "Hello world!\n"
    }
    // 刷新数据，真正写入文件
    writer.Flush()
}
```

## <a id="title9">9 协程和管道 [📜](#contents)</a>

### <a id="title9.1">9.1 协程概念辨析</a>

1. 进程(process)：资源分配的单位，在内存中会为每个进程分配不同的内存区域。

2. 线程(thread)：进程进一步细化为线程，是一个程序内部的一条执行路径。

3. 协程(goroutine)：又称为微线程、纤程，是一种用户态的轻量级线程。

   单线程下不可避免出现IO操作，但如果能在程序中（即用户程序级别，非操作系统级别）控制单线程下的多个任务能在一个任务遇到IO阻塞时就将寄存器上下文和栈保存到某个地方，然后切换到另外一个任务去计算；在任务切回来的时候，恢复先前保存的寄存器上下文和栈，从而保证该线程能够最大限度地处于就绪态（即随时都能被CPU执行的状态）相当于在用户程序级别将IO操作最大限度地隐藏起来，从而迷惑操作系统：该线程好像一直在计算，IO较少，从而将更多的CPU执行权限分配给该线程（**协程的本质是单线程**）
   **注意**：线程是CPU控制的，而协程是程序自身控制的，属于程序级别的切换，操作系统完全感知不到，因而更加轻量级。

### <a id="title9.2">9.2 主线程和协程执行流程</a>

若主线程退出了，即使协程还没执行完毕也会退出。

```go
func main() {
    for i := 1; i < 5; i++ {
        go func() {
            fmt.Println(i)
        }()
    }
    
    for j := 1; j < 5; j++ {
        go func(n int) {
            fmt.Println(n)
        }(j)
    }
    
    time.Sleep(time.Second * 2)
}
```

### <a id="title9.3">9.3 使用WaitGroup控制协程退出</a>

```go
var wg sync.WaitGroup
func main() {
    for i := 1; i < 5; i++ {
        wg.Add(1) // 协程开始wg+=1
        go func(n int) {
            defer wg.Done() // 协程执行完毕wg-=1
            fmt.Println(n)
        }(i)
    }
    // 主线程阻塞到wg=0为止
    wg.Wait()
}
```

### <a id="title9.4">9.4 使用互斥锁同步协程</a>

```go
var totalNum int
var wg sync.WaitGroup
var lock sync.Mutex
func add() {
    defer wg.Done()
    for i := 0; i < 100000; i++ {
        // 互斥锁性能、效率比较低
        lock.Lock()
        totalNum = totalNum + 1
        lock.Unlock()
    }
}
func sub() {
    defer wg.Done()
    for i := 0; i < 100000; i++ {
        lock.Lock()
        totalNum = totalNum - 1
        lock.Unlock()
    }
}
func main() {
    wg.Add(2)
    go add()
    go sub()
    wg.Wait()
    fmt.Println(totalNum)
}
```

### <a id="title9.5">9.5 读写锁</a>

RWMutex是读写锁，适用于读远远多于写次数的场景

```go
var lock sync.RWMutex
var wg sync.WaitGroup
func read() {
    defer wg.Done()
    lock.RLock()
    time.Sleep(time.Second)
    lock.RUnlock()
}
func write() {
    defer wg.Done()
    lock.Lock()
    time.Sleep(time.Second * 10)
    lock.Unlock()
}func main() {
    wg.Add(2)
    go add()
    go sub()
    wg.Wait()
    fmt.Println(totalNum)
}
func main() {
    wg.Add(6)
    for i := 0; i < 5; i++ {
        go read()
    }
    go write()
    wg.Wait()
}
```

### <a id="title9.6">9.6 管道</a>

#### <a id="title9.6.1">9.6.1 管道入门</a>

1. 管道的本质是一个队列
2. 管道自身线程安全，多协程操作同一管道不会发生资源争抢问题
3. 管道是有类型的，int类型管道只能写入int
4. 管道是引用类型，必须初始化(make后)才能写入数据

```go
var intChan chan int
var intChan1 chan<- int // 管道只写
var intChan2 <-chan int // 管道只读
intChan = make(chan int,3)
// 向管道存放数据(超出容量会报错)
intChan <- 10
// 读取管道中的数据(读空管道会报错)
num := <- intChan

// 关闭管道(关闭后不能写入，可以继续读取)
close(intChan)
```

#### <a id="title9.6.2">9.6.2 管道的遍历</a>

1. 支持for-range方式进行遍历
2. 遍历时，若管道没有关闭，会报deadlock错误

``` go
intChan := make(chan int,100)
for i := 0; i < 100; i++ {
    intChan <- i
}
close(intChan)
for v := range intChan {
    fmt.Println(v)
}
```

#### <a id="title9.6.3">9.6.3 协程和管道协同工作</a>

PS：这1章内容介绍不好，建议自己梳理

1. 开启一个writeData协程，向管道中写入50个整数；
2. 开启一个readData协程，从该管道中读取writeData写入的数据；

```go
var wg sync.WaitGroup
func writeData(intChan chan int) {
    defer wg.Done()
    for i := 1; i <= 50; i++ {
        intChan <- i
        time.Sleep(time.Second)
    }
    close(intChan)
}
func readData(intChan chan int) {
    defer wg.Done()
    for v := range intChan {
        fmt.Println(v)
        time.Sleep(time.Second)
    }
}

func main() {
    intChan := make(chan int,50)
    wg.Add(2)
    go writeData(intChan)
    go readData(intChan)
    wg.Wait()
}
```

#### <a id="title9.6.4">9.6.4 select功能</a>

解决多个管道的选择问题，可以从多个管道中随机公平地选择一个来执行

1. case后面必须是IO操作
2. default可以防止select被阻塞

```go
intChan := make(chan int,1)
go func() {
    time.Sleep(time.Second * 5)
    intChan <- 10
}()
strChan := make(chan string,1)
go func() {
    time.Sleep(time.Second * 2)
    strChan <- "string-10"
}()

select {
    case v := <- intChan :
        fmt.Println("intChan")
    case v := <- strChan :
        fmt.Println("stringChan")
}
```

## <a id="title10">10 网络编程 [📜](#contents)</a>

### <a id="title10.1">10.1 创建TCP客户端</a>

```go
package main
import(
    "fmt"
    "net"
    "bufio"
    "os"
)

func main() {
    fmt.Println("客户端启动...")
    conn,err := net.Dial("tcp","127.0.0.1:8888")
    if err != nil {
        fmt.Println("客户端连接失败:",err)
        return
    }
    fmt.Println("连接成功，conn:",conn)
    // 从终端读取一行用户输入信息
    reader := bufio.NewReader(os.Stdin) // os.Stdin代表终端标准输入
    str,err2 := reader.ReadString('\n')
    if err2 != nil {
        fmt.Println("终端输入失败:",err2)
    }
    // 将str数据发送给服务器
    n,err3 := conn.Write([]byte(str))
    if err3 != nil {
        fmt.Println("发送失败:",err3)
    }
    fmt.Println("终端数据通过客户端发送成功，一共发送%d字节的数据",n)
}
```

### <a id="title10.2">10.2 创建TCP服务器</a>

```go
package main
import(
    "fmt"
    "net"
)

func process(conn net.Conn) {
    defer conn.Close()
    for {
        buf := make([]byte,1024)
        n,err := conn.Read(buf)
        if err != nil {
            return
        }
        fmt.Println(string(buf[0:n]))
    }
}

func main() {
    fmt.Println("服务器启动...")
    listen,err := net.Listen("tcp",":8888")
    if err != nil {
        fmt.Println("监听失败:",err)
        return
    }
    for { // 循环监听
        conn,err2 := listen.Acceot()
        if err2 != nil {
            fmt.Println("客户端等待失败:",err2)
        } else {
            fmt.Printf("连接成功，conn=%v，客户端信息：%v \n",conn,conn.RemoteAddr().String())
        }
        // 协程处理客户端服务请求
        go process(conn)
    }
}
```

## <a id="title11">11 反射 [📜](#contents)</a>

### <a id="title11.1">11.1 对结构体进行反射</a>

```go
type Student struct {
    Name string
    Age int
}
func main() {
    stu := Student {
        Name : "丽丽",
        Age : 18,
    }
    TestReflect(stu)
}
func TestReflecT(i interface{}) {
    reType := reflect.TypeOf(i)
    fmt.Println("reType:",reType)
    k := reType.Kind() // 获取变量的类别
    // 类别 vs 类型：使用 %T 来获取变量的类型
    
    reValue := reflect.ValueOf(i)
    fmt.Println("reValue:",reValue)
    
    i2 := reValue.Interface()
    n,flag := i2.(Student)
    if flag == true { // 断言成功
        fmt.Printf("学生的名字是：%v",n.Name)
    }
}
```

### <a id="title11.2">11.2 通过反射操作结构体的属性和方法</a>

```go
func (s Student) Print() {
    fmt.Println("学生的名字是:",s.Name)
}
func TestStudentStruct(a interface{}) {
    val := reflect.ValueOf(a)
    n1 := val.NumField() // 结构体字段个数(2)
    for i := 0; i < n1; i++ {
        fmt.Printf("第%d个字段的值是：%v\n",i,val.Field(i))
    }
    n2 := val.NumMethod() // 结构体方法个数
    val.Method(0).Call(nil) // 方法按照ASCII的顺序排列
}
```

### <a id="title11.3">11.3 通过反射修改结构体变量的值</a>

```go
func main() {
    s := Student {
        Name : "丽丽",
        Age : 18,
    }
    TestStudentStruct(&s)
    fmt.Println(s)
}
func TestStudentStruct(a interface{}) {
    val := reflect.ValueOf(a)
    n := val.Elem().NumField()
    val.Elem().Field(0).SetString("张三")
}
```