# 字符串常用函数

| len(str)                            | 求长度         |
| ----------------------------------- | -------------- |
| +或fmt.Sprintf                      | 拼接字符串     |
| strings.Split                       | 分割           |
| strings.contains                    | 判断是否包含   |
| strings.HasPrefix,strings.HasSuffix | 前缀/后缀判断  |
| strings.Index(),strings.LastIndex() | 子串出现的位置 |
| strings.Join(a[]string, sep string) | join操作       |

# 流程控制

## for range（键值循环）

Go语言中可以使用`for range`遍历数组、切片、字符串、map 及通道（channel）。 通过`for range`遍历的返回值有以下规律：

1. 数组、切片、字符串返回索引和值。
2. map返回键和值。
3. 通道（channel）只返回通道内的值。

## goto(跳转到指定标签)

`goto`语句通过标签进行代码间的无条件跳转。`goto`语句可以在快速跳出循环、避免重复退出上有一定的帮助。Go语言中使用`goto`语句能简化一些代码的实现过程。 例如双层嵌套的for循环要退出时：

```go
func gotoDemo1() {
   var breakFlag bool
   for i := 0; i < 10; i++ {
      for j := 0; j < 10; j++ {
         if j == 2 {
            // 设置退出标签
            breakFlag = true
            break
         }
         fmt.Printf("%v-%v\n", i, j)
      }
      // 外层for循环判断
      if breakFlag {
         break
      }
   }
}

func gotoDemo2(){
   for i:=0; i<10; i++{
      for j:=0; j<10; j++{
         if j== 2{
            goto TheTag
         }
         fmt.Printf("%v-%v\n", i,j)
      }
   }
   TheTag:
      fmt.Println("结束for循环")
}
```

## break(跳出循环)

`break`语句可以结束`for`、`switch`和`select`的代码块。

`break`语句还可以在语句后面添加标签，表示退出某个标签对应的代码块，标签要求必须定义在对应的`for`、`switch`和 `select`的代码块上。 举个例子：

```go
func breakDemo1() {
BREAKDEMO1:
	for i := 0; i < 10; i++ {
		for j := 0; j < 10; j++ {
			if j == 2 {
				break BREAKDEMO1
			}
			fmt.Printf("%v-%v\n", i, j)
		}
	}
	fmt.Println("...")
}
```

## continue(继续下次循环)

`continue`语句可以结束当前循环，开始下一次的循环迭代过程，仅限在`for`循环内使用。

在 `continue`语句后添加标签时，表示开始标签对应的循环。

```go
func continueDemo() {
forloop1:
	for i := 0; i < 5; i++ {
		// forloop2:
		for j := 0; j < 5; j++ {
			if i == 2 && j == 2 {
				continue forloop1
			}
			fmt.Printf("%v-%v\n", i, j)
		}
	}
}
```

# 数组

## 基本定义

数组是同一种数据类型元素的集合。 在Go语言中，数组从声明时就确定，使用时可以修改数组成员，但是数组大小不可变化。 基本语法：

```go
// 定义一个长度为3元素类型为int的数组a
var a [3]int
```
## 遍历
```go
	for index, v := range a{
		fmt.Println(index,v)
	}
```

## 切片slice

### 定义

切片（Slice）是一个拥有相同类型元素的可变长度的序列。它是基于数组类型做的一层封装。它非常灵活，支持自动扩容。

声明切片类型的基本语法如下：

```go
var name []T
```

其中，

- name:表示变量名
- T:表示切片中的元素类型

**tip：**

- 对切片再执行切片表达式时（切片再切片），`high`的上限边界是切片的容量`cap(name)`，而不是长度len(name)。

### 动态创建切片make()

```bash
make([]T, size, cap)
```

其中：

- T:切片的元素类型
- size:切片中元素的数量
- cap:切片的容量

### 本质(底层数组封装)

切片的本质就是对底层数组的封装，它包含了三个信息：底层数组的指针、切片的长度（len）和切片的容量（cap）。

举个例子，现在有一个数组`a := [8]int{0, 1, 2, 3, 4, 5, 6, 7}`，切片`s1 := a[:5]`

![image-20220604105935740](/Users/alei/Documents/github.com/gostudy/base/base.assets/image-20220604105935740.png)

### 切片赋值拷贝

拷贝前后两个变量共享底层数组，对一个切片的修改会影响另一个切片的内容

```go
func main() {
	s1 := make([]int, 3) //[0 0 0]
	s2 := s1             //将s1直接赋值给s2，s1和s2共用一个底层数组
	s2[0] = 100
	fmt.Println(s1) //[100 0 0]
	fmt.Println(s2) //[100 0 0]
}
```

使用`copy(destSlice, srcSlice []T)`函数拷贝，再进行修改不影响源切片

### 切片删除指定索引元素

要从切片a中删除索引为`index`的元素，操作方法是`a = append(a[:index], a[index+1:]...)`

# map

map是一种无序的基于`key-value`的数据结构，Go语言中的map是引用类型，必须初始化才能使用。

```go
make(map[KeyType]ValueType, [cap])

func main() {
	scoreMap := make(map[string]int, 8)
	scoreMap["张三"] = 90
	scoreMap["小明"] = 100
	fmt.Println(scoreMap)
	fmt.Println(scoreMap["小明"])
	fmt.Printf("type of a:%T\n", scoreMap)
  
  // 也可在声明的时候填充元素
  userInfo := map[string]string{
		"username": "沙河小王子",
		"password": "123456",
	}
}
```

## 判断某个键是否存在

```go
	// 如果key存在ok为true,v为对应的值；不存在ok为false,v为值类型的零值
	v, ok := scoreMap["张三"]
	if ok {
		fmt.Println(v)
	} else {
		fmt.Println("查无此人")
	}
```

## 遍历

```go
func main() {
	scoreMap := make(map[string]int)
	scoreMap["张三"] = 90
	scoreMap["小明"] = 100
	scoreMap["娜扎"] = 60
	for k := range scoreMap {
		fmt.Println(k)
	}
}
```

## 删除

```go
delete(map, key)
```

## 按照指定顺序遍历map

```go
func main() {
	rand.Seed(time.Now().UnixNano()) //初始化随机数种子

	var scoreMap = make(map[string]int, 200)

	for i := 0; i < 100; i++ {
		key := fmt.Sprintf("stu%02d", i) //生成stu开头的字符串
		value := rand.Intn(100)          //生成0~99的随机整数
		scoreMap[key] = value
	}
	//取出map中的所有key存入切片keys
	var keys = make([]string, 0, 200)
	for key := range scoreMap {
		keys = append(keys, key)
	}
	//对切片进行排序
	sort.Strings(keys)
	//按照排序后的key遍历map
	for _, key := range keys {
		fmt.Println(key, scoreMap[key])
	}
}
```

# 函数

### 定义函数类型

我们可以使用`type`关键字来定义一个函数类型，具体格式如下：

```go
type calculation func(int, int) int
```

### 高阶函数

#### 函数作为参数

```go
func add(x, y int) int {
	return x + y
}
func calc(x, y int, op func(int, int) int) int {
	return op(x, y)
}
func main() {
	ret2 := calc(10, 20, add)
	fmt.Println(ret2) //30
}
```

#### 函数作为返回值

```go
func do(s string) (func(int, int) int, error) {
	switch s {
	case "+":
		return add, nil
	case "-":
		return sub, nil
	default:
		err := errors.New("无法识别的操作符")
		return nil, err
	}
}
```

### 匿名函数和闭包

#### 匿名函数

无函数名的函数

#### 闭包

变量`f`是一个函数并且它引用了其外部作用域中的`x`变量，此时`f`就是一个闭包。 在`f`的生命周期内，变量`x`也一直有效

```go
func adder() func(int) int {
	var x int
	return func(y int) int {
		x += y
		return x
	}
}
func main() {
	var f = adder()
	fmt.Println(f(10)) //10
	fmt.Println(f(20)) //30
	fmt.Println(f(30)) //60

	f1 := adder()
	fmt.Println(f1(40)) //40
	fmt.Println(f1(50)) //90
}
```

### defer延迟执行

`defer`语句会将其后面跟随的语句进行延迟处理。在`defer`归属的函数即将返回时，将延迟处理的语句按`defer`定义的逆序进行执行，也就是说，先被`defer`的语句最后被执行，最后被`defer`的语句，最先被执行。

方便的处理资源释放问题。比如：资源清理、文件关闭、解锁及记录时间等。

```go
func f1() int {
   x := 5
   defer func() {
      x++
   }()
   return x
}

func f2() (x int) {
   defer func() {
      x++
   }()
   return 5
}

func f3() (y int) {
   x := 5
   defer func() {
      x++
   }()
   return x
}
func f4() (x int) {
   defer func(x int) {
      x++
   }(x)
   return 5
}
func main() {
   fmt.Println(f1())  // 5
   fmt.Println(f2())// 6
   fmt.Println(f3())// 5
   fmt.Println(f4())// 5
}
```

**tip:**defer注册要延迟执行的函数时该函数所有的参数都需要确定其值

### 内置函数

| close          | 主要用来关闭channel                                          |
| -------------- | ------------------------------------------------------------ |
| len            | 用来求长度，比如string、array、slice、map、channel           |
| new            | 用来分配内存，主要用来分配值类型，比如int、struct。返回的是指针 |
| make           | 用来分配内存，主要用来分配引用类型，比如chan、map、slice     |
| append         | 用来追加元素到数组、slice中                                  |
| panic和recover | 用来做错误处理                                               |

#### panic/recover

```go
func f1(){
   fmt.Println("A")
}

func f2(){
   defer func() {
     err := recover()  // 仅在defer中有效，得到异常err，可判断err类型 switch err.(type)
      fmt.Println(err)
      if err != nil{
         fmt.Println("recover in func2")
      }
   }()
   panic("this is panic")
}
func f3(){
   fmt.Println("C")
}

func main() {
   f1()
   f2()
   f3()
}

//A
//this is panic
//recover in func2
//C

```

**tip**:

1. `recover()`必须搭配`defer`使用。
2. `defer`一定要在可能引发`panic`的语句之前定义。

# 指针

Go语言中的指针`&`（取地址）和`*`（根据地址取值）。

取变量指针的语法如下：

```go
ptr := &v    // v的类型为T
```

其中：

- v:代表被取地址的变量，类型为`T`
- ptr:用于接收地址的变量，ptr的类型就为`*T`，称做T的指针类型。*代表指针。

## 指针传值

```go
func modify1(x int) {
	x = 100
}

func modify2(x *int) {
	*x = 100
}

func main() {
	a := 10
	modify1(a)
	fmt.Println(a) // 10
	modify2(&a)
	fmt.Println(a) // 100
}
```

## new和make

对于引用类型的变量，我们在使用的时候不仅要声明它，还要为它分配内存空间，否则我们的值就没办法存储。

### new

new函数得到的是一个类型的指针，并且该指针对应的值为该类型的零值

```go
func main() {
	a := new(int)
	b := new(bool)
	fmt.Printf("%T\n", a) // *int
	fmt.Printf("%T\n", b) // *bool
	fmt.Println(*a)       // 0
	fmt.Println(*b)       // false
}	
```

### make

只用于slice、map以及chan的内存创建, 得到他们本身，他们本身就是引用类型

```go
func main() {
	var b map[string]int
	b = make(map[string]int, 10)  //初始化之后才能进行赋值
	b["沙河娜扎"] = 100
	fmt.Println(b)
}
```

# 结构体

## 类型

### 类型定义

通过`type`关键字的定义，`MyInt`就是一种新的类型，它具有`int`的特性。

```go
//将MyInt定义为int类型
type MyInt int
```

### 类型别名

`rune`和`byte`就是类型别名，他们的定义如下：

```go
type byte = uint8
type rune = int32
```

### 区别

```go
//类型定义
type NewInt int

//类型别名
type MyInt = int

func main() {
	var a NewInt
	var b MyInt
	
	fmt.Printf("type of a:%T\n", a) //type of a:main.NewInt
	fmt.Printf("type of b:%T\n", b) //type of b:int  类型别名编译后就没了
}
```

## 结构体

### 结构体字段的可见性

结构体中字段大写开头表示可公开访问，小写表示私有（仅在定义当前结构体的包中可访问）。

Go语言中通过`struct`来实现面向对象。

```go
type person struct {
		name string
  	gender string
		age int
	}
```

### 结构体实例化

```go
var 结构体实例 结构体类型
```

```go
func main() {
	var p1 person
	p1.name = "沙河娜扎"
	p1.gender = "女"
	p1.age = 18
	fmt.Printf("p1=%v\n", p1)  //p1={沙河娜扎 女 18}
	fmt.Printf("p1=%#v\n", p1) //p1=main.person{name:"沙河娜扎", gender:"女", age:18}
}
```

#### 匿名结构体（临时数据）

```go
func main() {
    var user struct{Name string; Age int}
    user.Name = "小王子"
    user.Age = 18
    fmt.Printf("%#v\n", user)
}
```

#### 创建指针类型结构体(new)

```go
var p2 = new(person)
fmt.Printf("%T\n", p2)     //*main.person
fmt.Printf("p2=%#v\n", p2) //p2=&main.person{name:"", gender:"", age:0}
```

tip：可以直接使用.语法直接访问结构体字段

#### 取结构体的地址实例化

`p1.name = "yl"`其实在底层是`(*p1).name = "yl"`，这是Go语言帮我们实现的语法糖。

```
p1 := &person{} // 相当于new(person)
p1.name = "yl"
p1.age =18
p1.gender = "男"
fmt.Printf("%#v", p1)
```

## 结构体初始化

没有初始化的结构体，其成员变量都是对应其类型的零值

### 使用键值对初始化

```go
p5 := person{
	name: "小王子",
	gender: "男",
	age:  18,
}
```

```go
p6 := &person{
	name: "小王子",
	gender: "男",
	age:  18,
}
```

### 使用值的列表初始化

```go
p8 := &person{
	"沙河娜扎",
	"女",
	28,
}
```

tip：

1. 必须初始化结构体的所有字段。
2. 初始值的填充顺序必须与字段在结构体中的声明顺序一致。
3. 该方式不能和键值初始化方式混用。

### For range 坑，map

```go
package main

import "fmt"

func main() {
    slice := []int{0, 1, 2, 3}
    myMap := make(map[int]*int)

    for index, value := range slice {
        myMap[index] = &value
    }
    fmt.Println("=====new map=====")
    prtMap(myMap)
}

func prtMap(myMap map[int]*int) {
    for key, value := range myMap {
        fmt.Printf("map[%v]=%v\n", key, *value)
    }
}
输出：
  =====new map=====
  map[3]=3
  map[0]=3
  map[1]=3
  map[2]=3
```

在迭代时，返回的变量value是一个迭代过程中根据切片依次赋值的新变量，所以值的地址总是相同的，导致结果不如预期。

## 构造函数

返回指针类型，开销小

```go
func newPerson(name, gender string, age int8) *person {
	return &person{
		name: name,
		gender: gender,
		age:  age,
	}
}
```

## 方法和接收者

```go
func (接收者变量 接收者类型) 方法名(参数列表) (返回参数) {
    函数体
}
```

```go
package main

import "fmt"

type person struct{
	name string
	age int8
}

func NewPerson(name string, age int8) *person {
	return &person{name: name, age: age}
}

func (p person) Dream (){
	fmt.Printf("%s正在做梦,梦见自己%d岁了", p.name, p.age)
}


func main() {
	p1 := NewPerson("yl", 18)
	p1.Dream()
}

```

#### 指针类型的接收者

```go
func (p *Person) SetAge (age int8){
   // 修改实例要传指针类型
   p.age = age
}
```

**什么时候应该使用指针类型接收者**

1. 需要修改接收者中的值
2. 接收者是拷贝代价比较大的大对象
3. 保证一致性，如果有某个方法使用了指针接收者，那么其他的方法也应该使用指针接收者。

## 结构体的匿名字段

```go
//Person 结构体Person类型
type Person struct {
	string
	int
}

func main() {
	p1 := Person{
		"小王子",
		18,
	}
	fmt.Printf("%#v\n", p1)        //main.Person{string:"北京", int:18}
	fmt.Println(p1.string, p1.int) //北京 18
}
```

tip：这里匿名字段的说法并不代表没有字段名，而是默认会采用类型名作为字段名，结构体要求字段名称必须唯一，因此一个结构体中同种类型的匿名字段只能有一个。

## 嵌套结构体

```go
//Address 地址结构体
type Address struct {
	Province string
	City     string
}

//User 用户结构体
type User struct {
	Name    string
	Gender  string
	Address Address
}

func main() {
	user1 := User{
		Name:   "小王子",
		Gender: "男",
		Address: Address{
			Province: "山东",
			City:     "威海",
		},
	}
	fmt.Printf("user1=%#v\n", user1)//user1=main.User{Name:"小王子", Gender:"男", Address:main.Address{Province:"山东", City:"威海"}}
}
```

### 嵌套匿名字段

```go
//Address 地址结构体
type Address struct {
	Province string
	City     string
}

//User 用户结构体
type User struct {
	Name    string
	Gender  string
	Address //匿名字段
}

func main() {
	var user2 User
	user2.Name = "小王子"
	user2.Gender = "男"
	user2.Address.Province = "山东"    // 匿名字段默认使用类型名作为字段名
	user2.City = "威海"                // 匿名字段可以省略
	fmt.Printf("user2=%#v\n", user2) //user2=main.User{Name:"小王子", Gender:"男", Address:main.Address{Province:"山东", City:"威海"}}
}
```

tip：当访问结构体成员时会先在结构体中查找该字段，找不到再去嵌套的匿名字段中查找。

### 嵌套结构体的字段名冲突

```go
//Address 地址结构体
type Address struct {
	Province   string
	City       string
	CreateTime string
}

//Email 邮箱结构体
type Email struct {
	Account    string
	CreateTime string
}

//User 用户结构体
type User struct {
	Name   string
	Gender string
	Address
	Email
}

func main() {
	var user3 User
	user3.Name = "沙河娜扎"
	user3.Gender = "男"
	// user3.CreateTime = "2019" //ambiguous selector user3.CreateTime
	user3.Address.CreateTime = "2000" //指定Address结构体中的CreateTime
	user3.Email.CreateTime = "2000"   //指定Email结构体中的CreateTime
}
```

### 结构体的“继承”

```go
//Animal 动物
type Animal struct {
	name string
}

func (a *Animal) move() {
	fmt.Printf("%s会动！\n", a.name)
}

//Dog 狗
type Dog struct {
	Feet    int8
	*Animal //通过嵌套匿名结构体实现继承
}

func (d *Dog) wang() {
	fmt.Printf("%s会汪汪汪~\n", d.name)
}

func main() {
	d1 := &Dog{
		Feet: 4,
		Animal: &Animal{ //注意嵌套的是结构体指针
			name: "乐乐",
		},
	}
	d1.wang() //乐乐会汪汪汪~
	d1.move() //乐乐会动！
}
```

## 结构体与JSON序列化

### 序列化

```go
	data, err := json.Marshal(c)
```

### 反序列化

```go
str := `{"Title":"101","Students":[{"ID":0,"Gender":"男","Name":"stu00"},{"ID":1,"Gender":"男","Name":"stu01"},{"ID":2,"Gender":"男","Name":"stu02"},{"ID":3,"Gender":"男","Name":"stu03"},{"ID":4,"Gender":"男","Name":"stu04"},{"ID":5,"Gender":"男","Name":"stu05"},{"ID":6,"Gender":"男","Name":"stu06"},{"ID":7,"Gender":"男","Name":"stu07"},{"ID":8,"Gender":"男","Name":"stu08"},{"ID":9,"Gender":"男","Name":"stu09"}]}`
	c1 := &Class{}
	err = json.Unmarshal([]byte(str), c1)
```

## 结构体tag

```go
//Student 学生
type Student struct {
	ID     int    `json:"id"` //通过指定tag实现json序列化该字段时的key
	Gender string //json序列化是默认使用字段名作为key
	name   string //私有不能被json包访问
}

func main() {
	s1 := Student{
		ID:     1,
		Gender: "男",
		name:   "沙河娜扎",
	}
	data, err := json.Marshal(s1)
	if err != nil {
		fmt.Println("json marshal failed!")
		return
	}
	fmt.Printf("json str:%s\n", data) //json str:{"id":1,"Gender":"男"}
}
```

## 结构体和方法补充

因为slice和map这两种数据类型都包含了指向底层数据的指针，因此我们在需要复制它们时要特别注意。

```go
type Person struct {
	name   string
	age    int8
	dreams []string
}

func (p *Person) SetDreams(dreams []string) {
	p.dreams = dreams
}

func main() {
	p1 := Person{name: "小王子", age: 18}
	data := []string{"吃饭", "睡觉", "打豆豆"}
	p1.SetDreams(data)

	// 你真的想要修改 p1.dreams 吗？
	data[1] = "不睡觉"
	fmt.Println(p1.dreams)  // ?
}
```

正确的做法是在方法中使用传入的slice的拷贝进行结构体赋值。

```go
func (p *Person) SetDreams(dreams []string) {
	p.dreams = make([]string, len(dreams))
	copy(p.dreams, dreams)
}
```

# 包与依赖管理

## 定义包

一个文件夹下面直接包含的文件只能归属一个包，同一个包的文件不能在多个文件夹下。包名为`main`的包是应用程序的入口包，这种包编译后会得到一个可执行文件，而编译不包含`main`包的源代码则不会得到可执行文件。

## 标识符可见性

通过标识符的首字母大/小写来控制标识符的对外可见（public）/不可见（private）的。在一个包内部只有首字母大写的标识符才是对外可见的。

```go
package demo

import "fmt"

// 包级别标识符的可见性

// num 定义一个全局整型变量
// 首字母小写，对外不可见(只能在当前包内使用)
var num = 100

// Mode 定义一个常量
// 首字母大写，对外可见(可在其它包中使用)
const Mode = 1

// person 定义一个代表人的结构体
// 首字母小写，对外不可见(只能在当前包内使用)
type person struct {
	name string
	Age  int
}

// Add 返回两个整数和的函数
// 首字母大写，对外可见(可在其它包中使用)
func Add(x, y int) int {
	return x + y
}

// sayHi 打招呼的函数
// 首字母小写，对外不可见(只能在当前包内使用)
func sayHi() {
	var myName = "七米" // 函数局部变量，只能在当前函数内使用
	fmt.Println(myName)
}
```

同样的规则也适用于结构体，结构体中可导出字段的字段名称必须首字母大写。

```go
type Student struct {
	Name  string // 可在包外访问的方法
	class string // 仅限包内访问的字段
}
```

### 包的引入

```go
import importname "path/to/package"
```

```go
import f "fmt"
f.Println("Hello world!")
```

```go
import _ "github.com/go-sql-driver/mysql"  // 匿名引入，会执行一次包中的init函数
```

### init初始化函数

在每一个Go源文件中，都可以定义任意个如下格式的特殊函数：

```go
func init(){
  // ...
}
```

这种特殊的函数不接收任何参数也没有任何返回值，我们也不能在代码中主动调用它，当程序启动的时候，init函数会按照它们声明的顺序自动执行。

## go module相关命令

|      命令       |                   介绍                   |
| :-------------: | :--------------------------------------: |
|   go mod init   |      初始化项目依赖，生成go.mod文件      |
| go mod download |          根据go.mod文件下载依赖          |
|   go mod tidy   | 比对项目文件中引入的依赖与go.mod进行比对 |
|  go mod graph   |              输出依赖关系图              |
|   go mod edit   |              编辑go.mod文件              |
|  go mod vendor  |     将项目的所有依赖导出至vendor目录     |
|  go mod verify  |        检验一个依赖包是否被篡改过        |
|   go mod why    |          解释为什么需要某个依赖          |

**GOPROXY**

```bash
go env -w GOPROXY=https://goproxy.cn,direct
```

设置了GOPROXY 之后，go 命令就会从配置的代理地址拉取和校验依赖包。

**GOPRIVATE**

引入了非公开的包（公司内部git仓库或 github 私有仓库等），此时便无法正常从代理拉取到这些非公开的依赖包，这个时候就需要配置 GOPRIVATE 环境变量

```bash
$ go env -w GOPRIVATE="git.mycompany.com"
```

**依赖保存位置**

Go module 会把下载到本地的依赖包会以类似下面的形式保存在 `$GOPATH/pkg/mod`目录下，每个依赖包都会带有版本号进行区分，这样就允许在本地存在同一个包的多个不同版本。

想清除所有本地已缓存的依赖包数据，可以执行 `go clean -modcache` 命令。

# 接口interface

## 接口（interface）是一种类型，一种抽象的类型

```go
type 接口类型名 interface{
    方法名1( 参数列表1 ) 返回值列表1
    方法名2( 参数列表2 ) 返回值列表2
    …
}
```

### 解决的问题

新增其他动物，只有实现了say方法，都是可以调用makeHungry来叫唤

```go
package main
import "fmt"

type Dog struct {}

func (d Dog) Say(){
   fmt.Println("汪汪汪~")
}

type Cat struct {}

func(c Cat ) Say(){
   fmt.Println("喵喵喵~")
}

type Sayer interface {
   Say ()
}

func makeHungry(s Sayer){
   s.Say()
}

func main() {
   c:= Cat{}
   makeHungry(c) // 喵喵喵~
   d:= Dog{}
   makeHungry(d) // 喵喵喵~
```

### 面向接口编程

- 比如一个网上商城可能使用支付宝、微信、银联等方式去在线支付，把它们当成“支付方式”来处理
- 比如三角形，四边形，圆形都能计算周长和面积，把它们当成“图形”来处理
- 比如满减券、立减券、打折券都属于电商场景下常见的优惠方式，把它们当成“优惠券”来处理

### 接口类型变量

```go
var x Sayer // 声明一个Sayer类型的变量x
a := Cat{}  // 声明一个Cat类型变量a
b := Dog{}  // 声明一个Dog类型变量b
x = a       // 可以把Cat类型变量直接赋值给x
x.Say()     // 喵喵喵
x = b       // 可以把Dog类型变量直接赋值给x
x.Say()     // 汪汪汪
```

## 值接收者和指针接收者

值接收者能实现实现接口的方法，可以将接口类型变量x赋值为 值变量或指针变量

指针接收者能实现实现接口的方法，仅可将接口类型变量x赋值为 指针变量，不可为值变量

## 类型与接口的关系

### 一个类型实现多个接口

例如：狗不仅可以叫，还可以动，分别定义`Sayer`接口和`Mover`接口

```go
// Sayer 接口
type Sayer interface {
	Say()
}

// Mover 接口
type Mover interface {
	Move()
}
type Dog struct {
	Name string
}

// 实现Sayer接口
func (d Dog) Say() {
	fmt.Printf("%s会叫汪汪汪\n", d.Name)
}

// 实现Mover接口
func (d Dog) Move() {
	fmt.Printf("%s会动\n", d.Name)
}
var d = Dog{Name: "旺财"}

var s Sayer = d
var m Mover = d

s.Say()  // 对Sayer类型调用Say方法
m.Move() // 对Mover类型调用Move方法
```

### 多种类型实现同一接口

例如：狗可以动，汽车也可以动

```go
// Car 汽车结构体类型
type Car struct {
	Brand string
}

// Move Car类型实现Mover接口
func (c Car) Move() {
	fmt.Printf("%s速度70迈\n", c.Brand)
}
// 把狗和汽车当成一个会动的类型来处理, 不需要关注是什么，会动就可以调用Move方法
var obj Mover

obj = Dog{Name: "旺财"}
obj.Move()

obj = Car{Brand: "宝马"}
obj.Move()
```

### 嵌入类型实现接口方法

接口的方法可以通过在类型中嵌入其他类型或者结构体来实现

例如：海尔洗衣机有甩干桶的功能，甩干桶又可以独立成一个类型

```go
type WashMachine interface {
   dry()
   wash()
}

type dryer struct{
}

func (d dryer) dry() {
   fmt.Println("正在甩干")
}

type Haier struct {
   dryer 
   name string
}

func (h Haier ) wash(){
   fmt.Println("正在洗衣服")
}

func main() {
   var wm WashMachine
   wm = Haier{name: "海尔洗衣机"}
   wm.wash()
   wm.dry()
}
```

## 接口组合

接口与接口之间可以通过互相嵌套形成新的接口类型，例如Go标准库`io`源码中就有很多接口之间互相组合的示例。

```go
// src/io/io.go

type Reader interface {
	Read(p []byte) (n int, err error)
}

type Writer interface {
	Write(p []byte) (n int, err error)
}

type Closer interface {
	Close() error
}

// ReadWriter 是组合Reader接口和Writer接口形成的新接口类型
type ReadWriter interface {
	Reader
	Writer
}

// ReadCloser 是组合Reader接口和Closer接口形成的新接口类型
type ReadCloser interface {
	Reader
	Closer
}

// WriteCloser 是组合Writer接口和Closer接口形成的新接口类型
type WriteCloser interface {
	Writer
	Closer
}
```

通过在结构体中嵌入一个接口类型，从而让该结构体类型实现了该接口类型，并且还可以改写该接口的方法。

```go
// src/sort/sort.go， TODO：未来查看，目前理解不了
```

## 空接口

空接口是指没有定义任何方法的接口类型。因此任何类型都可以视为实现了空接口。也正是因为空接口类型的这个特性，空接口类型的变量可以存储任意类型的值。

```go
type Any interface{}
// 简写
var x interface{}  // 声明一个空接口类型变量x
// x可以接收任意类型
```

### 空接口的应用

#### 空接口作为函数的参数

使用空接口实现可以接收任意类型的函数参数。

```go
// 空接口作为函数参数
func show(a interface{}) {
	fmt.Printf("type:%T value:%v\n", a, a)
}
```

#### 空接口作为map的值

使用空接口实现可以保存任意值的字典。

```go
// 空接口作为map值
	var studentInfo = make(map[string]interface{})
	studentInfo["name"] = "沙河娜扎"
	studentInfo["age"] = 18
	studentInfo["married"] = false
	fmt.Println(studentInfo)
```

## 接口值

接口值由“类型”和“值”组成，鉴于这两部分会根据存入值的不同而发生变化，我们称之为接口的`动态类型`和`动态值`。

![image-20220617230605698](/Users/alei/Documents/github.com/gostudy/base/base.assets/image-20220617230605698.png)

```go
var m Mover
```

此时，接口变量`m`是接口类型的零值，也就是它的类型和值部分都是`nil`

```go
fmt.Println(m == nil)  // true
```

```go
m = &Dog{Name: "旺财"}
```

此时，接口值`m`的动态类型会被设置为`*Dog`，动态值为结构体变量的拷贝。

然后，我们给接口变量`m`赋值为一个`*Car`类型的值。

```go
var c *Car
m = c
```

这一次，接口值`m`的动态类型为`*Car`，动态值为`nil`。

接口值是支持相互比较的，当且仅当接口值的动态类型和动态值都相等时才相等。

```go
var (
	x Mover = new(Dog)
	y Mover = new(Car)
)
fmt.Println(x == y) // false
```

Tip:不支持比较的类型，是不可以比较的，比如 切片

## 类型断言

```go
var m Mover

m = &Dog{Name: "旺财"}
fmt.Printf("%T\n", m) // *main.Dog

m = new(Car)
fmt.Printf("%T\n", m) // *main.Car
```

要从接口值中获取到对应的实际值需要使用类型断言，其语法格式如下。

```go
x.(T)
```

其中：

- x：表示接口类型的变量
- T：表示断言`x`可能是的类型。

返回两个参数，第一个参数是`x`转化为`T`类型后的变量，第二个值是一个布尔值

```go
var n Mover = &Dog{Name: "旺财"}
v, ok := n.(*Dog)
if ok {
	fmt.Println("类型断言成功")
	v.Name = "富贵" // 变量v是*Dog类型
} else {
	fmt.Println("类型断言失败")
}
```
如果对一个接口值有多个实际类型需要判断，推荐使用switch语句来实现。


```go
// justifyType 对传入的空接口类型变量x进行类型断言
func justifyType(x interface{}) {
	switch v := x.(type) {
	case string:
		fmt.Printf("x is a string，value is %v\n", v)
	case int:
		fmt.Printf("x is a int is %v\n", v)
	case bool:
		fmt.Printf("x is a bool is %v\n", v)
	default:
		fmt.Println("unsupport type！")
	}
}
```

tip:只有当有两个或两个以上的具体类型必须以相同的方式进行处理时才需要定义接口。切记不要为了使用接口类型而增加不必要的抽象，导致不必要的运行时损耗。

# Error接口和错误处理

Go 语言中把错误当成一种特殊的值来处理

 Error 方法会打印出错误的描述信息

```go
type error interface {
    Error() string
}
```

```go
func Open(name string) (*File, error) {
	return OpenFile(name, O_RDONLY, 0)
}
```

## 创建新错误信息

```go
var EOF = errors.New("EOF")
```

## 格式化描述错误信息fmt.Errorf

基于已有的错误再包装得到一个新的错误。

```go
fmt.Errorf("查询数据库失败，err:%w", err)
```

```go
// 判断错误类型  

switch err.(type) {
    case runtime.Error: // 运行时错误
         fmt.Println("runtime error:", err)
    default: // 非运行时错误
         fmt.Println("error:", err)
        }
```

## 错误结构体类型

此外我们还可以自己定义结构体类型，实现``error`接口。

```go
// OpError 自定义结构体类型
type OpError struct {
	Op string
}

// Error OpError 类型实现error接口
func (e *OpError) Error() string {
	return fmt.Sprintf("无权执行%s操作", e.Op)
}
```

# 反射

反射就是在运行时动态的获取一个变量的类型信息和值信息。

## reflect包

### TypeOf

得任意值的类型对象（reflect.Type）

```go
func reflectType(x interface{}) {
	v := reflect.TypeOf(x)
	fmt.Printf("type:%v\n", v)
}
```

#### Type.name Type.kind

name就是类型名字，kind是底层类型

```go
func reflectType(x interface{}) {
	t := reflect.TypeOf(x)
	fmt.Printf("type:%v kind:%v\n", t.Name(), t.Kind())
}
```

```go
func main() {
	var a *float32 // 指针
	var b myInt    // 自定义类型
	var c rune     // 类型别名
	reflectType(a) // type: kind:ptr
	reflectType(b) // type:myInt kind:int64
	reflectType(c) // type:int32 kind:int32

	type person struct {
		name string
		age  int
	}
	type book struct{ title string }
	var d = person{
		name: "沙河小王子",
		age:  18,
	}
	var e = book{title: "《跟小王子学Go语言》"}
	reflectType(d) // type:person kind:struct
	reflectType(e) // type:book kind:struct
```

Go语言的反射中像数组、切片、Map、指针等类型的变量，它们的`.Name()`都是返回`空`。

### ValueOf

`reflect.ValueOf()`返回的是`reflect.Value`类型，其中包含了原始值的值信息。`reflect.Value`与原始值之间可以互相转换。

`reflect.Value`类型提供的获取原始值的方法如下：

|           方法           |                             说明                             |
| :----------------------: | :----------------------------------------------------------: |
| Interface() interface {} | 将值以 interface{} 类型返回，可以通过类型断言转换为指定类型  |
|       Int() int64        |     将值以 int 类型返回，所有有符号整型均可以此方式返回      |
|      Uint() uint64       |     将值以 uint 类型返回，所有无符号整型均可以此方式返回     |
|     Float() float64      | 将值以双精度（float64）类型返回，所有浮点数（float32、float64）均可以此方式返回 |
|       Bool() bool        |                     将值以 bool 类型返回                     |
|     Bytes() []bytes      |               将值以字节数组 []bytes 类型返回                |
|     String() string      |                     将值以字符串类型返回                     |

#### 通过反射获取值

```go
func reflectValue(x interface{}) {
	v := reflect.ValueOf(x)
	k := v.Kind()
	switch k {
	case reflect.Int64:
		// v.Int()从反射中获取整型的原始值，然后通过int64()强制类型转换
		fmt.Printf("type is int64, value is %d\n", int64(v.Int()))
	case reflect.Float32:
		// v.Float()从反射中获取浮点型的原始值，然后通过float32()强制类型转换
		fmt.Printf("type is float32, value is %f\n", float32(v.Float()))
	case reflect.Float64:
		// v.Float()从反射中获取浮点型的原始值，然后通过float64()强制类型转换
		fmt.Printf("type is float64, value is %f\n", float64(v.Float()))
	}
}
func main() {
	var a float32 = 3.14
	var b int64 = 100
	reflectValue(a) // type is float32, value is 3.140000
	reflectValue(b) // type is int64, value is 100
	// 将int类型的原始值转换为reflect.Value类型
	c := reflect.ValueOf(10)
	fmt.Printf("type c :%T\n", c) // type c :reflect.Value
}
```

#### 通过反射设置变量的值

注意函数参数传递的是值拷贝，必须传递变量地址才能修改变量值。而反射中使用专有的`Elem()`方法来获取指针对应的值。

```go
package main

import (
	"fmt"
	"reflect"
)

func reflectSetValue1(x interface{}) {
	v := reflect.ValueOf(x)
	if v.Kind() == reflect.Int64 {
		v.SetInt(200) //修改的是副本，reflect包会引发panic
	}
}
func reflectSetValue2(x interface{}) {
	v := reflect.ValueOf(x)
	// 反射中使用 Elem()方法获取指针对应的值
	if v.Elem().Kind() == reflect.Int64 {
		v.Elem().SetInt(200)
	}
}
func main() {
	var a int64 = 100
	// reflectSetValue1(a) //panic: reflect: reflect.Value.SetInt using unaddressable value
	reflectSetValue2(&a)
	fmt.Println(a)
}
```

### 结构体反射

`reflect.Type`中与获取结构体成员相关的的方法如下表所示。

|                            方法                             |                             说明                             |
| :---------------------------------------------------------: | :----------------------------------------------------------: |
|                  Field(i int) StructField                   |          根据索引，返回索引对应的结构体字段的信息。          |
|                       NumField() int                        |                   返回结构体成员字段数量。                   |
|        FieldByName(name string) (StructField, bool)         |       根据给定字符串返回字符串对应的结构体字段的信息。       |
|            FieldByIndex(index []int) StructField            | 多层成员访问时，根据 []int 提供的每个结构体的字段索引，返回字段的信息。 |
| FieldByNameFunc(match func(string) bool) (StructField,bool) |              根据传入的匹配函数匹配需要的字段。              |
|                       NumMethod() int                       |                返回该类型的方法集中方法的数目                |
|                     Method(int) Method                      |                返回该类型方法集中的第i个方法                 |
|             MethodByName(string)(Method, bool)              |              根据方法名返回该类型方法集中的方法              |

## 反射是把双刃剑

反射是一个强大并富有表现力的工具，能让我们写出更灵活的代码。但是反射不应该被滥用，原因有以下三个。

1. 基于反射的代码是极其脆弱的，反射中的类型错误会在真正运行的时候才会引发panic，那很可能是在代码写完的很长时间之后。
2. 大量使用反射的代码通常难以理解。
3. 反射的性能低下，基于反射实现的代码通常比正常代码运行速度慢一到两个数量级。

# 并发

Go语言中的并发程序主要是通过基于CSP（communicating sequential processes）的goroutine和channel来实现，当然也支持使用传统的多线程共享内存的并发方式。

```go
package main

import (
	"fmt"
	"sync"
)

var wg sync.WaitGroup

func hello(i int) {
	defer wg.Done() // goroutine结束就登记-1
	fmt.Println("hello", i)
}
func main() {
	for i := 0; i < 10; i++ {
		wg.Add(1) // 启动一个goroutine就登记+1
		go hello(i)
	}
	wg.Wait() // 等待所有登记的goroutine都结束
}
```

### 动态栈

操作系统的线程一般都有固定的栈内存（通常为2MB）,而 Go 语言中的 goroutine 非常轻量级，一个 goroutine 的初始栈空间很小（一般为2KB），所以在 Go 语言中一次创建数万个 goroutine 也是可能的。并且 goroutine 的栈不是固定的，可以根据需要动态地增大或缩小， Go 的 runtime 会自动为 goroutine 分配合适的栈空间。

### goroutine调度

#### 操作系统调度

从一个线程切换到另一个线程需要完整的上下文切换。因为可能需要多次内存访问，索引这个切换上下文的操作开销较大，会增加运行的cpu周期

#### goroutine调度

Go语言运行时（runtime）层面的实现，是完全由 Go 语言本身实现的一套调度系统——go scheduler。它的作用是按照一定的规则将所有的 goroutine 调度到操作系统线程上执行。

调度器采用的是 `GPM` 调度模型

![image-20220619102919312](/Users/alei/Documents/github.com/gostudy/base/base.assets/image-20220619102919312.png)

### GOMAXPROCS

Go语言中可以通过`runtime.GOMAXPROCS`函数设置当前程序并发时占用的 CPU逻辑核心数。默认计算机核心数

## channel

Go语言采用的并发模型是`CSP（Communicating Sequential Processes）`，提倡**通过通信共享内存**而不是**通过共享内存而实现通信**



共享内存进行数据交换，但是共享内存在不同的 goroutine 中容易发生竞态问题。为了保证数据交换的正确性，很多并发模型中必须使用互斥量对内存进行加锁，这种做法势必造成性能问题。

### 初始化

```go
make(chan 元素类型, [缓冲大小])
```

```go
ch <- 10 // 把10发送到ch中
x := <- ch // 从ch中接收值并赋值给变量x
<-ch       // 从ch中接收值，忽略结果
value, ok := <- ch // 多返回值，ok代表是否关闭

func f3(ch chan int) {  // for range接收值
	for v := range ch {
		fmt.Println(v)
	}
}

close(ch)
```

关闭后的通道有以下特点：

1. 对一个关闭的通道再发送值就会导致 panic。
2. 对一个关闭的通道进行接收会一直获取值直到通道为空。
3. 对一个关闭的并且没有值的通道执行接收操作会得到对应类型的零值。
4. 关闭一个已经关闭的通道会导致 panic。

### 无缓冲通道

使用无缓冲通道进行通信将导致发送和接收的 goroutine 同步化。因此，无缓冲通道也被称为`同步通道`

### 单向通道

```go
<- chan int // 只接收通道，只能接收不能发送
chan <- int // 只发送通道，只能发送不能接收
```

在函数传参及任何赋值操作中全向通道（正常通道）可以转换为单向通道，但是无法反向转换。

```go
var ch4 = make(chan int, 1)
ch4 <- 10
var ch5 <-chan int // 声明一个只接收通道ch5
ch5 = ch4          // 变量赋值时将ch4转为单向通道
<-ch5
```

## work pool



开启多个goroutine 执行多个任务





## select多路复用

同时响应多个通道的操作， 实现从多个通道接收值的需求

```go
select {
case <-ch1:
	//...
case data := <-ch2:
	//...
case ch3 <- 10:
	//...
default:
	//默认操作
}
```

- 可处理一个或多个 channel 的发送/接收操作。
- 如果多个 case 同时满足，select 会**随机**选择一个执行。
- 对于没有 case 的 select 会一直阻塞，可用于阻塞 main 函数，防止退出。

## 并发安全-互斥锁

sync.Mutex

```go
var (
	x int64

	wg sync.WaitGroup // 等待组

	m sync.Mutex // 互斥锁
)

// add 对全局变量x执行5000次加1操作
func add() {
	for i := 0; i < 5000; i++ {
		m.Lock() // 修改x前加锁
		x = x + 1
		m.Unlock() // 改完解锁
	}
	wg.Done()
}

func main() {
	wg.Add(2)

	go add()
	go add()

	wg.Wait()
	fmt.Println(x)
}
```

### 读写互斥锁

sync.RWMutex

| func (rw *RWMutex) Lock()           | 获取写锁                       |
| ----------------------------------- | ------------------------------ |
| func (rw *RWMutex) Unlock()         | 释放写锁                       |
| func (rw *RWMutex) RLock()          | 获取读锁                       |
| func (rw *RWMutex) RUnlock()        | 释放读锁                       |
| func (rw *RWMutex) RLocker() Locker | 返回一个实现Locker接口的读写锁 |

## sync.WaitGroup

内部维护着一个计数器，计数器的值可以增加和减少。例如当我们启动了 N 个并发任务时，就将计数器值增加N。每个任务完成时通过调用 Done 方法将计数器减1。通过调用 Wait 来等待并发任务执行完，当计数器值为 0 时，表示所有并发任务已经完成。

## sync.Once

即使在高并发的场景下也只会被执行一次，例如只加载一次配置文件等。

```go
func (o *Once) Do(f func())
```

### 并发安全的单例模式

下面是借助`sync.Once`实现的并发安全的单例模式：

```go
package singleton

import (
    "sync"
)

type singleton struct {}

var instance *singleton
var once sync.Once

func GetInstance() *singleton {
    once.Do(func() {
        instance = &singleton{}
    })
    return instance
}
```

## sync.Map

Go 语言中内置的 map 不是并发安全的，go提供了并发安全的MAP

| func (m *Map) Store(key, value interface{})                  | 存储key-value数据               |
| ------------------------------------------------------------ | ------------------------------- |
| func (m *Map) Load(key interface{}) (value interface{}, ok bool) | 查询key对应的value              |
| func (m *Map) LoadOrStore(key, value interface{}) (actual interface{}, loaded bool) | 查询或存储key对应的value        |
| func (m *Map) LoadAndDelete(key interface{}) (value interface{}, loaded bool) | 查询并删除key                   |
| func (m *Map) Delete(key interface{})                        | 删除key                         |
| func (m *Map) Range(f func(key, value interface{}) bool)     | 对map中的每个key-value依次调用f |

## 原子操作

通常直接使用原子操作比使用锁操作效率更高。Go语言中原子操作由内置的标准库`sync/atomic`提供。





## errgroup

将一个任务拆分成多个子任务交给多个 goroutine 去运行，这时我们该如何获取到子任务可能返回的错误呢

它能为处理公共任务的子任务而开启的一组 goroutine 提供同步、error 传播和基于context 的取消功能。

errgroup.Group 提供了`Go`和`Wait`两个方法。

```go
func (g *Group) Go(f func() error)
```

- Go 函数会在新的 goroutine 中调用传入的函数f。
- 第一个返回非零错误的调用将取消该Group；下面的Wait方法会返回该错误

```go
func (g *Group) Wait() error
```

- Wait 会阻塞直至由上述 Go 方法调用的所有函数都返回，然后从它们返回第一个非nil的错误（如果有）。
