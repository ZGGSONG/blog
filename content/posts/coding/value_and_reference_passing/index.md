---
title: "值传递和引用传递 | Go"
date: 2022-04-10T18:27:39+08:00
description: ""
featuredImage: ""
categories:
- coding
tags:
- golang
---

## 值传递和引用传递

### 说明

值传递：在调用函数时将实际参数复制一份传递到函数中，函数修改参数将不会影响源实际参数。
引用传递：调用函数时将实际参数的地址传递到函数中，在函数中对参数所进行的修改将影响源实际参数。

**go 语言中只存在值传递**

> 虽然 slice、map、chan 等这些类型时引用类型，但是传递这些参数时仍然为值传递

```go
func main() {
   slice := []int{1, 2, 3, 4}
   m := make(map[int]string)
   m[0] = "a"
   var i *int
   a := 123
   i = &a
   fmt.Printf("[main slice] %p\t%v\n", &slice, slice)
   fmt.Printf("[main map] %p\t%v\n", &m, m)
   fmt.Printf("[main pointer] %p\t%v\n", &i, *i)
   tt(slice, m, i)
}

func tt(s []int, m map[int]string, i *int) {
   fmt.Printf("[tt slice] %p\t%v\n", &s, s)
   fmt.Printf("[tt map] %p\t%v\n", &m, m)
   fmt.Printf("[tt pointer] %p\t%v\n", &i, *i)
   s[0] = 999
   m
}

//output
[main slice] 0x1400000c0a8	[1 2 3 4]
[main map] 0x1400000e038	map[0:a]
[main pointer] 0x1400000e040	123

[tt slice] 0x1400000c108	[1 2 3 4]
[tt map] 0x1400000e048	map[0:a]
[tt pointer] 0x1400000e050	123

[main slice] 0x1400000c0a8	[999 2 3 4]
[main map] 0x1400000e038	map[0:b]
[main pointer] 0x1400000e040	999
```

根据此发现传递时 slice、map、pointer 地址都发生了变化，所以传递的是实参的拷贝，即值传递

但是在 `tt` 函数中修改 slice、map、pointer 的值时，函数外的值也发生了变化
首先指针没得说，直接修改了指针所指向内存地址中的值
其次就是 slice 和 map 了，这与其存储结构有很大关系，下面以 slice 结构为例

### slice 结构

![[Pasted image 20230309104134.png]]

`slice`是一个包含三个字段的结构体，它们分别是：

1.  指向底层数组的指针（ptr）：指向`slice`关联的底层数组的第一个元素。
2.  `slice`的长度（len）：表示`slice`中元素的数量。
3.  `slice`的容量（cap）：表示`slice`从第一个元素开始到底层数组末尾的元素数。
    > 注意 `slice` 本身不存储任何数据，他只是底层数组的引用。当对 slice 进行修改时，实际上事修改底层数组中相应位置的元素，所以可能存在多个 slice 对应一个底层数组，并对其中一个 slcie 所做修改也会影响其他 slice
    > **在 go 语言中，slice、map、channel 都是引用类型，都会有这些特性**

### slice append 机制

1.  如果添加的元素数量小于等于当前`slice`的剩余容量，则直接将新元素添加到`slice`的末尾，同时更新`slice`的长度（len）。
2.  如果添加的元素数量大于当前`slice`的剩余容量，则需要重新分配内存。新的内存大小通常为原内存大小的 2 倍或更高，具体取决于实现。然后将原有的元素和新增的元素复制到新的内存中，并更新`slice`的长度和容量（cap）。
3.  将新元素添加到`slice`的末尾。

举个例子：

```go
func main() {
   s := make([]int, 5) // 创建一个长度为 5 的 int 类型 Slice   fmt.Printf("add=%p len=%d cap=%d data=%v\n", s, len(s), cap(s), s)

   s = append(s, 1, 2, 3) // 向 Slice 添加 3 个新元素
   fmt.Printf("add=%p len=%d cap=%d data=%v\n", s, len(s), cap(s), s)

   s = append(s, 4, 5) // 再次添加 2 个新元素
   fmt.Printf("add=%p len=%d cap=%d data=%v\n", s, len(s), cap(s), s)
}

//output
add=0x14000018300 len=5 cap=5 data=[0 0 0 0 0]
add=0x1400001c0a0 len=8 cap=10 data=[0 0 0 0 0 1 2 3]
add=0x1400001c0a0 len=10 cap=10 data=[0 0 0 0 0 1 2 3 4 5]
```

### 总结

1. go 语言中函数传递都是值传递
2. slice、map、channel 都是引用类型，值存储都是对底层数据结构的指针
3. slice 扩容的时候会分配新的底层数组来存储扩容后的元素，与原来的底层数组不同。再来修改 slice 就不会对源参数 slice 造成影响