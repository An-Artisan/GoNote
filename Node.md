#Go圣经笔记


## 1 入门
### HelloWorld



```
 // gopl.io/ch1/helloworld
 package main
 
 import "fmt"
 
 func main() {
     fmt.Println("Hello, 世界")
 }
 ```
 
 
* go run helloworld.go #编译并运行helloword.go文件

* go build helloworld.go #编译helloworld.go文件，会生成一个helloworld.exe文件

* go get gopl.io/ch1/helloworld  #拉取远程代码保存在本地，会在本地生成对应的目录


* Go语言的代码通过包（package）组织，包类似于其它语言里的库（libraries）或者模块（modules）。一个包由位于单个目录下的一个或多个.go源代码文件组成, 目录定义包的作用。每个源文件都以一条package声明语句开始，这个例子里就是package main, 表示该文件属于哪个包，紧跟着一系列导入（import）的包，之后是存储在这个文件里的程序语句。


* Go语言的程序入口是main包->main函数


* 必须恰当导入需要的包，缺少了必要的包或者导入了不需要的包，程序都无法编译通过。这项严格要求避免了程序开发过程中引入未使用的包（译注：Go语言编译过程没有警告信息，争议特性之一）。

* import声明必须跟在文件的package声明之后。


* Go语言不需要在语句或者声明的末尾添加分号，除非一行上有多条语句。实际上，编译器会主动把特定符号后的换行符转换为分号,因此换行符添加的位置会影响Go代码的正确解析（译注：比如行末是标识符、整数、浮点数、虚数、字符或字符串文字、关键字break、continue、 fallthrough或return中的一个、运算符和分隔符++、--、)、]或}中的一个）。 举个例子, 函数的左括号{必须和func函数声明在同一行上, 且位于末尾， 不能独占一行，而在表达式x + y中，可在+后换行，不能在+前换行（译注：以+结尾的话不会被插入分号分隔符，但是以x结尾的话则会被分号分隔符，从而导致编译错误）。


### 命令行参数

```
// gopl.io/ch1/echo1
// Echo1 prints its command-line arguments. 
package main

import (
    "fmt"
    "os"
)

func main() {
    var s, sep string
    for i := 1; i < len(os.Args); i++ {
        s += sep + os.Args[i]
        sep = " "
    }
    fmt.Println(s)
}

``` 
* var声明定义了两个string类型的变量s和sep。变量会在声明时直接初始化。如果变量没有显式初始化，则被隐式地赋予其类型的零值（zero value），数值类型是0，字符串类型是空字符串 " "。（注意：golang中没有单引号作为字符串）

* 自增语句i++给i加1；这和i += 1以及i = i + 1都是等价的。对应的还有i--给i减1。它们是语句，而不像C系的其它语言那样是表达式。所以j = i++非法，而且++和--都只能放在变量名后面，因此--i也非法。（Golang中没有前缀和后缀之分）



* for循环的另一种形式, 在某种数据类型的区间（range）上遍历，如字符串或切片。echo的第二版本展示了这种形式：
```
// gopl.io/ch1/echo2
// Echo2 prints its command-line arguments.
package main

import (
    "fmt"
    "os"
)

func main() {
    s, sep := "", ""
    for _, arg := range os.Args[1:] {
        s += sep + arg
        sep = " "
    }
    fmt.Println(s)
}

```
每次循环迭代，range产生一对值；索引以及在该索引处的元素值。

``` 
s := ""
var s string
var s = ""
var s string = ""
```

用哪种不用哪种，为什么呢？第一种形式，是一条短变量声明，最简洁，但只能用在函数内部，而不能用于包变量。第二种形式依赖于字符串的默认初始化零值机制，被初始化为""。第三种形式用得很少，除非同时声明多个变量。第四种形式显式地标明变量的类型，当变量类型与初值类型相同时，类型冗余，但如果两者类型不同，变量类型就必须了。实践中一般使用前两种形式中的某个，初始值重要的话就显式地指定变量的类型，否则使用隐式初始化。

如果连接涉及的数据量很大，这种方式代价高昂。一种简单且高效的解决方案是使用strings包的Join函数：

``` 

// gopl.io/ch1/echo3
func main() {
    fmt.Println(strings.Join(os.Args[1:], " "))
}
fmt.Println(os.Args[1:]) # 直接打印调试os.Args的值

```

### 查找重复的行

```
 gopl.io/ch1/dup1
 
 // Dup1 prints the text of each line that appears more than
 // once in the standard input, preceded by its count.
 package main
 
 import (
     "bufio"
     "fmt"
     "os"
 )
 
 func main() {
     counts := make(map[string]int)
     input := bufio.NewScanner(os.Stdin)
     for input.Scan() {
         counts[input.Text()]++
     }
     // NOTE: ignoring potential errors from input.Err()
     for line, n := range counts {
         if n > 1 {
             fmt.Printf("%d\t%s\n", n, line)
         }
     }
 }
 
 ```
 
* map存储了键/值（key/value）的集合，对集合元素，提供常数时间的存、取或测试操作。键可以是任意类型，只要其值能用==运算符比较，最常见的例子是字符串；值则可以是任意类型。这个例子中的键是字符串，值是整数。内置函数make创建空map，此外，它还有别的作用。
* （译注：从功能和实现上说，Go的map类似于Java语言中的HashMap，Python语言中的dict，Lua语言中的table，通常使用hash实现。遗憾的是，对于该词的翻译并不统一，数学界术语为映射，而计算机界众说纷纭莫衷一是。为了防止对读者造成误解，保留不译。）
* 注意 map是无序的，延伸：hash实现的数据结构都是无序的



## 2 程序结构

### 2.1 命名
* golang 推荐驼峰式命名

### 2.2 声明

``` 
 // Boiling prints the boiling point of water.
 package main
 
 import "fmt"
 
 const boilingF = 212.0
 
 func main() {
     var f = boilingF
     var c = (f - 32) * 5 / 9
     fmt.Printf("boiling point = %g°F or %g°C\n", f, c)
     // Output:
     // boiling point = 212°F or 100°C
 }
 
```


其中常量boilingF是在包一级范围声明语句声明的，然后f和c两个变量是在main函数内部声明的声明语句声明的。在包一级声明语句声明的名字可在整个包对应的每个源文件中访问，而不是仅仅在其声明语句所在的源文件中访问。
* 相比之下，局部声明的名字就只能在函数内部很小的范围被访问。





### 2.3 变量

变量定义
```
var 变量名字 类型 = 表达式
```

* 其中“类型”或“= 表达式”两个部分可以省略其中的一个。如果省略的是类型信息，那么将根据初始化表达式来推导变量的类型信息。如果初始化表达式被省略，那么将用零值初始化该变量。 数值类型变量对应的零值是0，布尔类型变量对应的零值是false，字符串类型对应的零值是空字符串，接口或引用类型（包括slice、指针、map、chan和函数）变量对应的零值是nil。数组或结构体等聚合类型对应的零值是每个元素或字段都是对应该类型的零值。

* 一组变量也可以通过调用一个函数，由函数返回的多个返回值初始化：
  
```
var f, err = os.Open(name) // os.Open returns a file and an error

```

* 一个指针的值是另一个变量的地址。一个指针对应变量在内存中的存储位置。并不是每一个值都会有一个内存地址，但是对于每一个变量必然有对应的内存地址。通过指针，我们可以直接读或更新对应变量的值，而不需要知道该变量的名字（如果变量有名字的话）。


* （细读） 如果用“var x int”声明语句声明一个x变量，那么&x表达式（取x变量的内存地址）将产生一个指向该整数变量的指针，指针对应的数据类型是*int，指针被称之为“指向int类型的指针”。如果指针名字为p，那么可以说“p指针指向变量x”，或者说“p指针保存了x变量的内存地址”。同时*p表达式对应p指针指向的变量的值。一般*p表达式读取指针指向的变量的值，这里为int类型的值，同时因为*p对应一个变量，所以该表达式也可以出现在赋值语句的左边，表示更新指针所指向的变量的值

``` 
x := 1
p := &x         // p, of type *int, points to x
fmt.Println(*p) // "1"
*p = 2          // equivalent to x = 2
fmt.Println(x)  // "2"

```


* 对于聚合类型每个成员——比如结构体的每个字段、或者是数组的每个元素——也都是对应一个变量，因此可以被取地址。


* 任何类型的指针的零值都是nil。如果p指向某个有效变量，那么p != nil测试为真。指针之间也是可以进行相等测试的，只有当它们指向同一个变量或全部是nil时才相等。

```
var x, y int
fmt.Println(&x == &x, &x == &y, &x == nil) // "true false false"
```
* 在Go语言中，返回函数中局部变量的地址也是安全的。例如下面的代码，调用f函数时创建局部变量v，在局部变量地址被返回之后依然有效，因为指针p依然引用这个变量。

``` 
var p = f()

func f() *int {
    v := 1
    return &v
}
```
* 每次调用f函数都将返回不同的结果，因为是v是临时生成的变量

```
fmt.Println(f() == f()) // "false"

```


### 2.3.3  new函数

另一个创建变量的方法是调用用内建的new函数。表达式new(T)将创建一个T类型的匿名变量，初始化为T类型的零值，然后返回变量地址，返回的指针类型为*T。

```

p := new(int)   // p, *int 类型, 指向匿名的 int 变量
fmt.Println(*p) // "0"
*p = 2          // 设置 int 匿名变量的值为 2
fmt.Println(*p) // "2"

```

* 每次调用new函数都是返回一个新的变量的地址，因此下面两个地址是不同的：

```

p := new(int)
q := new(int)
fmt.Println(p == q) // "false"

```


* 当然也可能有特殊情况：如果两个类型都是空的，也就是说类型的大小是0，例如struct{}和 [0]int, 有可能有相同的地址（依赖具体的语言实现）（译注：请谨慎使用大小为0的类型，因为如果类型的大小为0的话，可能导致Go语言的自动垃圾回收器有不同的行为，具体请查看runtime.SetFinalizer函数相关文档）。
  
* new函数使用通常相对比较少，因为对于结构体来说，直接用字面量语法创建新变量的方法会更灵活
  
  
### 2.3.4. 变量的生命周期


* 编译器会自动选择在栈上还是在堆上分配局部变量的存储空间，但可能令人惊讶的是，这个选择并不是由用var还是new声明变量的方式决定的。

```  
var global *int

func f() {
    var x int
    x = 1
    global = &x
}

func g() {
    y := new(int)
    *y = 1
}

```

* f函数里的x变量必须在堆上分配，因为它在函数退出后依然可以通过包一级的global变量找到，虽然它是在函数内部定义的；用Go语言的术语说，这个x局部变量从函数f中逃逸了。相反，当g函数返回时，变量*y将是不可达的，也就是说可以马上被回收的。因此，*y并没有从函数g中逃逸，编译器可以选择在栈上分配*y的存储空间（译注：也可以选择在堆上分配，然后由Go语言的GC回收这个变量的内存空间），虽然这里用的是new方式。其实在任何时候，你并不需为了编写正确的代码而要考虑变量的逃逸行为，要记住的是，逃逸的变量需要额外分配内存，同时对性能的优化可能会产生细微的影响。

* Go语言的自动垃圾收集器对编写正确的代码是一个巨大的帮助，但也并不是说你完全不用考虑内存了。你虽然不需要显式地分配和释放内存，但是要编写高效的程序你依然需要了解变量的生命周期。例如，如果将指向短生命周期对象的指针保存到具有长生命周期的对象中，特别是保存到全局变量时，会阻止对短生命周期对象的垃圾回收（从而可能影响程序的性能）。

* 注意：堆栈缓存方式 

* ①栈使用的是一级缓存， 他们通常都是被调用时处于存储空间中，调用完毕立即释放。
* ②堆则是存放在二级缓存中，生命周期由虚拟机的垃圾回收算法来决定（并不是一旦成为孤儿对象就能被回收）。所以调用这些对象的速度要相对来得低一些。


### 2.4.1. 元组赋值

``` 
x, y = y, x

a[i], a[j] = a[j], a[i]

```
* 上面代码是交换x,y的值。


### 2.5类型

```

// Package tempconv performs Celsius and Fahrenheit temperature computations.
package tempconv

import "fmt"

type Celsius float64    // 摄氏温度
type Fahrenheit float64 // 华氏温度

const (
    AbsoluteZeroC Celsius = -273.15 // 绝对零度
    FreezingC     Celsius = 0       // 结冰点温度
    BoilingC      Celsius = 100     // 沸水温度
)

func CToF(c Celsius) Fahrenheit { return Fahrenheit(c*9/5 + 32) }

func FToC(f Fahrenheit) Celsius { return Celsius((f - 32) * 5 / 9) }
```

```
fmt.Printf("%g\n", BoilingC-FreezingC) // "100" °C

boilingF := CToF(BoilingC)

fmt.Printf("%g\n", boilingF-CToF(FreezingC)) // "180" °F

fmt.Printf("%g\n", boilingF-FreezingC)       // compile error: type mismatch
```


``` 
var c Celsius
var f Fahrenheit
fmt.Println(c == 0)          // "true"
fmt.Println(f >= 0)          // "true"
fmt.Println(c == f)          // compile error: type mismatch
fmt.Println(c == Celsius(f)) // "true"!
```
* 如果两个值有着不同的类型，则不能直接进行比较和计算

### 2.6包和文件

* 每个包都对应一个独立的名字空间。例如，在image包中的Decode函数和在unicode/utf16包中的 Decode函数是不同的。要在外部引用该函数，必须显式使用image.Decode或utf16.Decode形式访问。

* 包还可以让我们通过控制哪些名字是外部可见的来隐藏内部实现信息。在Go语言中，一个简单的规则是：如果一个名字是大写字母开头的，那么该名字是导出的（译注：因为汉字不区分大小写，因此汉字开头的名字是没有导出的）。

* 每个源文件都是以包的声明语句开始，用来指明包的名字。当包被导入的时候，包内的成员将通过类似tempconv.CToF的形式访问。而包级别的名字，例如在一个文件声明的类型和常量，在同一个包的其他源文件也是可以直接访问的，就好像所有代码都在一个文件一样。
 
 
### 2.6.2. 包的初始化

* 如果包中含有多个.go源文件，它们将按照发给编译器的顺序进行初始化，Go语言的构建工具首先会将.go文件根据文件名排序，然后依次调用编译器编译。


* 对于在包级别声明的变量，如果有初始化表达式则用表达式初始化，还有一些没有初始化表达式的，例如某些表格数据初始化并不是一个简单的赋值过程。在这种情况下，我们可以用一个特殊的init初始化函数来简化初始化工作。每个文件都可以包含多个init初始化函数

```
func init() { /* ... */ }
``` 


### 2.7. 作用域

* 一个声明语句将程序中的实体和一个名字关联，比如一个函数或一个变量。声明语句的作用域是指源代码中可以有效使用这个名字的范围。

* 不要将作用域和生命周期混为一谈。声明语句的作用域对应的是一个源代码的文本区域；它是一个编译时的属性。一个变量的生命周期是指程序运行时变量存在的有效时间段，在此时间区域内它可以被程序的其他部分引用；是一个运行时的概念。

* 句法块是由花括弧所包含的一系列语句，就像函数体或循环体花括弧包裹的内容一样。句法块内部声明的名字是无法被外部块访问的。

* 对于导入的包，例如tempconv导入的fmt包，则是对应源文件级的作用域，因此只能在当前的文件中访问导入的fmt包，当前包的其它源文件无法访问在当前源文件导入的包。

