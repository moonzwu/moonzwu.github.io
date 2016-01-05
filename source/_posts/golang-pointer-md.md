title: Go语言中的指针
date: 2015-11-18 13:00:30
tags: Golang
---
Go语言中有指针，这并不稀奇，但是你无法进行指针运算。这么做的主要目的是在对内存高效运算的同时又兼顾复杂性的问题。由于结构体在Go中是一个非常常见的数据结构，经常被用于计算。如果你直接将结构体作为变量在函数中进行传递，那么内存的利用效率是比较低下的，因为默认情况下Go中的函数参数是传值的，也就是说你的结构体多大，运行时就会分配并拷贝一遍。显然这种做法在结构体小的时候没什么问题，但实际的代码运行中结构体往往很大（几十KB以上是很正常的）。这个时候为了减少无谓的分配与复制动作，传递指针是一个好主意。现代语言都有这个能力，如Java中的是引用，Go则采用指针。

# 指针的用法
Go采用了与C语言一样的标记符号来描述指针：**&取地址**，***取值**。

```
client := &http.Client{}
resp, err := client.Get("http://www.baidu.com")
```
一个对结构体操作的例子：

```
type Car struct {
	Weight int
	Brand  string
}


func main() {
	printCar := func(pCar *Car) {
		fmt.Println("Car brand is ", pCar.Brand, 
					  ", Car weight is ", pCar.Weight)
		pCar.Weight++
	}

	structpointer := func() {
		p := &Car{
			Weight: 10,
			Brand:  "Ford",
		}

		printCar(p)
		printCar(p)

	}
	
	structpointer()
}
```

除了使用&地址符来获取指针以外，你也可以使用new操作符来操作。

```
printCar := new(Car)
```

new操作符有一个特点，它不会进行任何的初始化操作。所以你不能够期待在new的同时进行初始化，但是它会把对象的内存空间都设置成0（对于字符串类型的则是空串）。当然你也可以使用“组合语法”来进行对象的创建与初始化操作，在逻辑上new操作符等价于声明对象并取地址。

```
car := Car{
	Weight: 10,
	Brand:  "Ford",
}
```

在Go里面还有另外一个操作符make，也干了类似于new的操作，但是它们有着显著的不同。
new操作符即可以用于结构体也可以用于接口。而make操作符则只能用于Array, Slice和Map三个内建集合对象。
