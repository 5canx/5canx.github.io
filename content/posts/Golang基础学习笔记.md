---
title: "Golang基础学习笔记"
date: 2024-06-18T18:30:07+08:00
draft: true
description:
keywords:
license:
weight: 0
tags:
  - Golang
categories:
  - Golang
---
<!--more-->

## Golang的接口是一种抽象的类型

> Go语言中的接口（interface）是一组方法签名的集合，是一种抽象类型。接口定义了方法，但没有实现，而是由具体的类型（struct）实现这些方法，因此接口是一种实现多态的机制。

### 接口定义
```go
type 接口名称er interface{
    func(arg1 类型,arg2 类型2) 返回值
    func(arg3 类型3) 返回值3
}  //最好以er结尾表示  不加func也可以
```

### 结构体定义

```go
type 结构体名称 struct{
  Name 类型 //（string、int、bool等）
}
```

### 典型Demo

```go
package main

import (
	"fmt"
)

// Worker 接口定义了一个方法 Work
type Worker interface {
	Work()
}

// programmer 结构体代表一个程序员,定义数据类型
type programmer struct {
	Java   string
	Linux  string
	Python string
}

// Work 实现了 Worker 接口的方法
func (p *programmer) Work() {
	// 输出程序员的类型和使用的编程语言
	fmt.Printf("小李正在学习%s\n", p.Java)
	fmt.Printf("小张正在学习%s\n", p.Linux)
	fmt.Printf("小王正在学习%s\n", p.Python)
}

func main() {
	// 创建一个 programmer 实例，并初始化字段
	myProgrammer := programmer{
		Java:   "Java语言",
		Linux:  "Linux运维",
		Python: "Python语言",
	}
	var Myworker Worker = &myProgrammer // 接口变量赋值也叫实现结构体
	Myworker.Work() //调用接口方法
}
```

{{< admonition info >}}
**以 上 代 码 作 为 基 础 进 行 拓 展** 
{{< /admonition >}}

### 一个类型实现多个接口

```go
package main

import (
	"fmt"
	"time"
)

// Worker 接口定义了一个方法 Work
type Worker interface {
	Work()
}

type Stoper interface {
	Stop()
}

// programmer 结构体代表一个程序员
type programmer struct {
	Java   string
	Linux  string
	Python string
}

// Work 实现了 Worker 接口的方法
func (p *programmer) Work() { //指针接收者可以修改字段值
	// 输出程序员的类型和使用的编程语言
	fmt.Printf("小李正在学习%s\n", p.Java)
	fmt.Printf("小张正在学习%s\n", p.Linux)
	fmt.Printf("小王正在学习%s\n", p.Python)
}
func (p programmer) Stop() { //值接收者不修改字段值
	fmt.Printf("小李已经停止学习%s\n", p.Java)
	fmt.Printf("小张已经停止学习%s\n", p.Linux)
	fmt.Printf("小王已经停止学习%s\n", p.Python)
}
func main() {
	// 创建一个 programmer 实例，并初始化字段
	myProgrammer := programmer{
		Java:   "Java语言",
		Linux:  "Linux运维",
		Python: "Python语言",
	}
	x1 := programmer{} // 接口变量赋值也叫实现结构体

	var Myworker Worker = &myProgrammer // 接口变量赋值也叫实现结构体
	Myworker.Work()                     //调用接口方法
	time.Sleep(time.Second * 2)
	x1.Stop()
}
```

### 多个类型实现一个接口

```go
package main

import (
	"fmt"
)

// Worker 接口定义了一个方法 Work
type Worker interface {
	Work()
}

// programmer 结构体代表一个程序员
type programmer struct {
	Python string
}
type older struct {
	age int
}

func (p programmer) Work() {
	fmt.Printf("%s的程序员正在工作", p.Python)
	fmt.Println("") //换行
}
func (o older) Work() {
	fmt.Printf("%d岁的程序员正在工作", o.age)
}
func main() {
	// 创建一个 programmer 实例，并初始化字段
	myProgrammer := programmer{
		Python: "Python",
	}
	o := older{age: 20}
	var Myworker Worker = &myProgrammer // 接口变量赋值也叫实现结构体
	Myworker.Work()                     //调用接口方法
	o.Work()
}
```



### 接口的嵌套

```go
package main

import (
	"fmt"
)

// Worker 接口定义了一个方法 Work
type Worker interface {
	Work()
}
type Moyuer interface {
	Moyu()
}
type Peoler interface {
	Worker
	Moyuer
}
type Computer struct {
	something string
}

func (c *Computer) Work() {
	fmt.Printf("%s都要工作\n", c.something)
}
func (c *Computer) Moyu() {
	fmt.Printf("%s也要摸鱼\n", c.something)
}

func main() {
	sth := Computer{something: "程序员"}
	x := &sth
	x.Work()
	x.Moyu()
}
```



### 空接口

{{< admonition info >}}
空接口类型的变量可以存储任意类型的变量。
{{< /admonition >}}

```go
package main

import (
	"fmt"
)

func main() {
	var x interface{}
	v1 := "程序猿"
	v2 := 200
	v3 := true
	x = v1
	fmt.Printf("%T类型 : %v\n", x, x)
	x = v2
	fmt.Printf("%T类型 : %v\n", x, x)
	x = v3
	fmt.Printf("%T类型 : %v\n", x, x)
}
```

{{< admonition info >}}
因为可以存储任意类型的变量，所以我们把他当做方法中的参数使用。
{{< /admonition >}}

```go
package main

import (
	"fmt"
)

func test(w interface{}) {
	fmt.Printf("Hello %s", w)
}
func main() {
	words := "Golang"
	test(words)
} //Hello Golang
```



### 类型断言

{{< admonition info >}}
类型断言的作用就是判断空接口的值和值的类型

{{< /admonition >}}

```go
package main

import (
	"fmt"
)

func main() {
	var words interface{} = "Golang"
    switch value := words.(type) {            //语法格式为：x.(T)
	case string:
		fmt.Printf("String类型，值为%v", value)
	case int:
		fmt.Printf("Int类型，值为%v", value)
	case bool:
		fmt.Printf("Bool类型，值为%v", value)
	}

}
```



