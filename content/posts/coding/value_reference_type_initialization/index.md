---
title: "值类型、引用类型及初始化类型 | Go"
date: 2022-03-09T16:21:02+08:00
description: ""
featuredImage: ""
categories:
- coding
tags:
- golang
---

## 值类型与引用类型

### 值类型

#### 包含

1.  布尔型（bool）
2.  整型（int、int8、int16、int32、int64、uint、uint8、uint16、uint32、uint64）
3.  浮点型（float32、float64）
4.  复数类型（complex64、complex128）
5.  字符串类型（string）
6.  数组类型（array）
7.  结构体类型（struct）
8.  函数类型（func）
9.  接口类型中的值类型「即没有包含指针类型的接口」（interface）
    > 除此之外，还可以使用 type 关键字自定义其他值类型。需要注意的是，值类型在进行赋值或传递时是按值传递的，也就是说会进行一次复制操作

#### 特点

1.  值类型的变量存储的是实际的数据，而非指向数据的指针。
2.  值类型的变量在进行赋值或传递时，会进行一次复制操作。
3.  因为值类型的变量每个都拥有自己的独立副本，所以它们之间的操作互不干扰。
4.  内存通常在栈中分配，值类型通常比引用类型更加高效，它们不需要额外的内存分配和 GC(垃圾回收)操作。

    > 需要注意的是，在处理大型数据集合时，由于值类型的复制操作可能会导致内存和 CPU 开销较大，因此建议使用`slice`、`map`等引用类型来避免这种情况。

### 引用类型

#### 包含

1.  切片类型（slice）
2.  映射类型（map）
3.  通道类型（channel）
4.  接口类型「包含指针类型的接口」（interface）

> 除此之外，可以使用`struct`等复合类型来自定义其他引用类型。
> 需要注意的是，引用类型在进行赋值或传递时，并不会产生数据的副本。它们实际上是指向数据结构的指针，因此对它们的操作会直接影响到原始数据结构。这也意味着当多个引用类型指向同一个数据结构时，它们之间的操作会互相影响。

#### 特点

1.  引用类型的变量不存储实际的数据，而是存储指向数据结构的指针。
2.  引用类型的变量在进行赋值或传递时，不会进行复制操作，而是共享同一个底层数据结构。
3.  因为多个引用类型变量可能会共享同一个底层数据结构，所以对其中任何一个变量所做的修改都会影响到其他变量。
4.  引用类型通常比值类型更加灵活和扩展性强，因为它们可以动态地增加或减少底层数据结构的大小。
5.  内存通常分配在堆上分配，通过 GC 回收

#### 指针类型的接口举例

在 Golang 中，接口类型中如果包含指针类型的方法，则该接口属于引用类型。这是因为，接口变量在存储时只是存储了一个指向底层数据结构的指针，并没有将底层数据结构本身复制到接口变量中。因此，对接口变量的修改会影响底层数据结构和其他引用该数据结构的变量。

例如，下面的代码定义了一个 `Animal` 接口和一个 `Dog` 结构体实现了该接口：

```go
type Animal interface {
    Speak() string
}

type Dog struct {
    name string
}

func (d *Dog) Speak() string {
    return "Woof!"
}
```

在上面的代码中，`Speak()` 方法使用了指针类型的接收者。因此，`Dog` 类型实现的 `Animal` 接口属于引用类型。

我们可以创建一个 `Dog` 类型的变量 `d`，并将其赋值给一个类型为 `Animal` 的变量 `a`：

```go
d := &Dog{"Fido"}
var a Animal = d
```

此时，`a` 变量存储的是指向 `d` 变量的指针，而不是 `d` 变量本身。因此，对 `a` 变量的修改也会影响 `d` 变量的值。

```go
d.name = "Max"
fmt.Println(a.(*Dog).name) // 输出: "Max"
```

> 在 Golang 中，如果接口类型中所有的方法都是值类型的方法，则该接口也是值类型。
> 对于空接口`interface{}`，它可以表示任何类型的值，因此在使用空接口时需要特别小心。如果没有正确地处理类型断言等操作，可能会导致运行时错误。

在 Go 语言中，可以使用类型断言和反射等机制来获取接口变量的具体类型信息。以下是一个使用类型断言和反射实现通用数据结构的例子：

```go
package main

import (
    "fmt"
    "reflect"
)

type item struct {
    value interface{}
}

func main() {
    s := []item{
        {1},
        {"foo"},
        {3.14},
    }

    for _, i := range s {
        // 使用类型断言获取具体类型
        switch v := i.value.(type) {
        case int:
            fmt.Println("int:", v)
        case string:
            fmt.Println("string:", v)
        case float64:
            fmt.Println("float64:", v)
        default:
            fmt.Println("unknown type")
        }

        // 使用反射获取具体类型
        t := reflect.TypeOf(i.value)
        switch t.Kind() {
        case reflect.Int:
            fmt.Println("int:", i.value)
        case reflect.String:
            fmt.Println("string:", i.value)
        case reflect.Float64:
            fmt.Println("float64:", i.value)
        default:
            fmt.Println("unknown type")
        }
    }
}
```

> 虽然使用类型断言和反射可以方便地获取接口变量的具体类型信息，但是过度使用会导致代码的可读性和维护性降低，因此需要谨慎使用。

## 初始化类型

### 说明

`字面值初始化` 使用花括号括起来的值列表来初始化结构体、数组和切片等类型

`new`用于分配内存，并返回指向该内存地址的指针。`new(T)`返回一个指向新分配的、类型为`T`的零值的指针。

> 在 Golang 中，指针本身也是一种值类型。但是，在指针变量被使用时，它所指向的数据结构就具有了引用类型的特性。

`make`则是用于创建引用类型（如`slice`、`map`和`channel`）的对象，并进行相关的初始化操作。它返回的是该类型的引用，而非指针。

这两个函数的作用虽然略有相似，但用法和场景完全不同。使用`new`函数通常是为了分配一块动态内存，而使用`make`函数是为了创建某些具有特殊性质的引用类型（如`slice`、`map`和`channel`），并对其进行初始化。

### 举例

以下是一些具体的应用举例：

1. `字面值初始化`

```go
// 初始化一个字符串切片
s := []string{"apple", "banana", "orange"}

// 初始化一个结构体
type Person struct {
    Name string
    Age int
}

p := Person{
    Name: "John",
    Age: 30,
}
```

2.  使用`new`函数创建一个指向类型为`int`的零值的指针：

```go
x := new(int)
fmt.Println(*x)   // 输出: 0
```

3.  使用`make`函数创建一个`slice`并进行初始化：

```go
s := make([]int, 0, 10)   // 创建一个长度为 0、容量为 10 的 int 类型 slice
s = append(s, 1, 2, 3)    // 向 slice 中添加元素
fmt.Println(s)           // 输出: [1 2 3]
```

4.  使用`make`函数创建一个`map`对象并进行初始化：

```go
m := make(map[string]int)   // 创建一个 string -> int 的 map
m["a"] = 1                  // 添加键值对 m["b"] = 2
fmt.Println(m)             // 输出: map[a:1 b:2]
```

5.  使用`make`函数创建一个带缓冲的`channel`：

```go
ch := make(chan int, 10)    // 创建一个能容纳 10 个 int 类型元素的 channel
ch <- 1                     // 发送消息到 channel
fmt.Println(<-ch)          // 从 channel 中接收消息并打印出来
```

需要注意的是，在上述代码中，`new`函数只是分配了一块零值内存，并返回一个指向该内存地址的指针。如果要对其进行赋值操作，还需要通过解引用（`*`）运算符访问该指针所指向的值。

而`make`函数则直接返回了一个可以使用的引用类型，因此可以直接对其进行相关操作。
