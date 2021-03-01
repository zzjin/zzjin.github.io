---
title: "golang泛型在btree的应用与优化"
date: 2022-06-18T00:30:04+08:00
draft: false
toc: true
images:
tags:
  - golang
  - generics
  - btree
---

近几个月更新的 golang1.18 支持了泛型,正好前两天看到了 google 官方的 golang-btree 更新了 v2,使用泛型`generics`代替了`interface{}`,号称提升了 **40%** 的性能

## Show me the code

https://github.com/google/btree

### 重新整理后的函数级别的完整的diff ([原始MR点这里](https://github.com/google/btree/pull/45)):

<br />
<details>
  <summary>展开函数层面的完整diff</summary>

  <style>
    .side-diff {width:96vw;margin-left: -23vw;background-color: white;font-size: 13px;color: #998;}
    .side-diff table th, table td {padding: 0;border: 0;}
  </style>
  <div class="side-diff">
    {{< include-html "static/content/2022/btree-diff.html" >}}
  </div>

</details>

<br />

## 简述:

google实现的[btree](https://github.com/google/btree)包,本身其实已经做了很多的优化,其中每个外部可以调用的函数都有对应的Benchmark,这对我们分析泛型带来的优势提供了很大的帮助.

本文不讨论btree本身的优劣,单纯的从一个可以进行数据操作的集合函数库来着重讨论泛型带来的golang性能优化.

如果对golang的1.18新带来的泛型(generics)不太了解,可以先阅读一下 https://go.dev/doc/tutorial/generics (官方) 或者 https://segmentfault.com/a/1190000041634906 (中文讲解)


## 实现解析:
1. 如上文diff代码能看到的,泛型化的btree代码,主要就是将原来的 `item`/`node` 替换成 `item[T]`/`node[T]`
```diff
@@ -72,32 +82,27 @@
-type FreeList struct {
+type FreeListG[T any] struct {
 	mu       sync.Mutex
-	freelist []*node
+	freelist []*node[T]
 }
 
 // size is the maximum size of the returned free list.
-func NewFreeList(size int) *FreeList {
-	return &FreeList{freelist: make([]*node, 0, size)}
+func NewFreeListG[T any](size int) *FreeListG[T] {
+	return &FreeListG[T]{freelist: make([]*node[T], 0, size)}
 }
 
-func (f *FreeList) newNode() (n *node) {
+func (f *FreeListG[T]) newNode() (n *node[T]) {
 	f.mu.Lock()
 	index := len(f.freelist) - 1
 	if index < 0 {
 		f.mu.Unlock()
-		return new(node)
+		return new(node[T])
 	}
 	n = f.freelist[index]
 	f.freelist[index] = nil
```

这一块就是标准的泛型更新,不需要特别的说明.

2. 全新定义了泛型里面可以被排序的类型集合与他们的generics的比较函数
```golang
// LessFunc[T] 函数是用来定义泛型的类型形参(Type parameter) 'T'是如何比较大小的.
// 返回的顺序必须是严密的,且当'a' < 'b'是返回true.
type LessFunc[T any] func(a, b T) bool

// Ordered 定义了golang原生可以被比较大小的内置类型
// @Note: 这里主要是做前向兼容,如果有自定义的类型,实现了`Less`方法也可以用
type Ordered interface {
	~int | ~int8 | ~int16 | ~int32 | ~int64 | ~uint | ~uint8 | ~uint16 | ~uint32 | ~uint64 | ~float32 | ~float64 | ~string
}

// Less[T] 就是内置的Less函数,直接返回支持的泛型类型形参的比较结果
func Less[T Ordered]() LessFunc[T] {
	return func(a, b T) bool { return a < b }
}
```
相对的,老版本直接是定义了一个`Item`的`interface{}`来实现同样的Less函数
```golang
// Item 表示树上的一个独立对象.
type Item interface {
	// Less 函数必须要实现判断当前值是否小于传递进来的值.
	// 相等的值只会保存一个
	Less(than Item) bool
}
```

3. 修改内部函数的返回值
```diff
@@ -308,70 +290,70 @@
-func (n *node) insert(item Item, maxItems int) Item {
-	i, found := n.items.find(item)
+func (n *node[T]) insert(item T, maxItems int) (_ T, _ bool) {
+	i, found := n.items.find(item, n.cow.less)
 	if found {
 		out := n.items[i]
 		n.items[i] = item
-		return out
+		return out, true
 	}
 	if len(n.children) == 0 {
 		n.items.insertAt(i, item)
-		return nil
+		return
 	}
```
```diff
@@ -424,8 +406,9 @@
 		// 在这里使用的了一个内部的'remove'调用并且传递了一个'maxItem'参数,
 		// 拉取i的前一项(最右边的子节点)并设置到拉取的当前节点上
-		n.items[i] = child.remove(nil, minItems, removeMax)
-		return out
+		var zero T
+		n.items[i], _ = child.remove(zero, minItems, removeMax)
+		return out, true
 	}
```
之前的函数实现返回`Item`,`Item`是一个`interface{}`(1.18之后是一个基本接口),之前可以通过直接返回一个`nil`表示没有找到数据或者操作失败

但是泛型之后,不同的类型形参不能表示这种状态,所以在返回泛型的形参`T`之外,还增加返回了一个`bool`类型来表示操作结果

**这里的`Item`(实际是一个`interface{}`接口)是性能瓶颈与优化的核心,后续我们回详细展开聊到**

4. 最后再增加亿点点兼容代码
```golang
// 前向兼容的方式,定义泛型下的'BTree'是一个'BTreeG'的Item约束
type BTree BTreeG[Item]

// 定义默认的函数,就是上文#2定义好的比较函数的实例
var itemLess LessFunc[Item] = func(a, b Item) bool {
	return a.Less(b)
}

// New 创建一个新的'BTree',内部转换为创建一个'BTreeG[item]',同时传递less函数
func New(degree int) *BTree {
	return (*BTree)(NewG[Item](degree, itemLess))
}
...更多的函数
```

### 性能测试结果

MR里面提到使用泛型改造之后,对于官方的bench函数(int存储),性能能优化40%+!

这里写一个简单的Bench来对比两种实现方式的性能

#### Code
```golang
package main

import (
	"math/rand"
	"testing"
	"time"

	"github.com/google/btree"
)

const treeSize = 50000

func init() {
	rand.Seed(time.Now().Unix())
}

func BenchmarkBTree(b *testing.B) {
	b.StopTimer()
	insertP := rand.Perm(treeSize)
	b.StartTimer()
	i := 0
	for i < b.N {
		tr := btree.New(32)
		for _, item := range insertP {
			tr.ReplaceOrInsert(btree.Int(item))
			i++
			if i >= b.N {
				return
			}
		}
	}
}

func BenchmarkBTreeG(b *testing.B) {
	b.StopTimer()
	insertP := rand.Perm(treeSize)
	b.StartTimer()
	i := 0
	for i < b.N {
		tr := btree.NewOrderedG[int](32)
		for _, item := range insertP {
			tr.ReplaceOrInsert(item)
			i++
			if i >= b.N {
				return
			}
		}
	}
}

```
Bench执行之后的结果对比(使用github-codespace机器)
```
Running tool: /usr/local/go/bin/go test -benchmem -run=^$ -bench ^(BenchmarkBTree|BenchmarkBTreeG)$ gh/btree -count=1 -failfast

goos: linux
goarch: amd64
pkg: gh/btree
cpu: Intel(R) Xeon(R) Platinum 8272CL CPU @ 2.60GHz
BenchmarkBTree-4    	 3254396	       373.8 ns/op	      44 B/op	       1 allocs/op
BenchmarkBTreeG-4   	 5487584	       215.7 ns/op	      19 B/op	       0 allocs/op
PASS
ok  	gh/btree	5.106s
```

完整的官方全部bench对比结果:(再次感叹下原来版本的btree已经非常优化了,alloc(s)已经很小)

```shell
go test -bench -count=5 -failfast > [old|new].txt
benchstst old.txt new.txt
```

<br />

| name | old time/op | new time/op | delta | |
| ----------- | ----------- | ----------- | ----------- | ----------- |
|  Insert-20                             |   175ns ± 1%  |   131ns ± 1% |  -25.23% | (p=0.008 n=5+5) |
|  Seek-20                               |   141ns ± 2%  |    82ns ± 6% |  -42.10% | (p=0.008 n=5+5) |
|  DeleteInsert-20                       |   357ns ± 1%  |   272ns ± 1% |  -24.01% | (p=0.008 n=5+5) |
|  DeleteInsertCloneOnce-20              |   354ns ± 1%  |   269ns ± 1% |  -24.01% | (p=0.008 n=5+5) |
|  DeleteInsertCloneEachTime-20          |  1.53µs ± 1%  |  0.97µs ± 7% |  -36.70% | (p=0.008 n=5+5) |
|  Delete-20                             |   196ns ± 1%  |   145ns ± 2% |  -25.91% | (p=0.008 n=5+5) |
|  Get-20                                |   155ns ± 2%  |   108ns ± 0% |  -30.03% | (p=0.008 n=5+5) |
|  GetCloneEachTime-20                   |   239ns ± 1%  |   180ns ± 2% |  -24.78% | (p=0.008 n=5+5) |
|  Ascend-20                             |  47.7µs ± 1%  |  35.0µs ± 1% |  -26.63% | (p=0.008 n=5+5) |
|  Descend-20                            |  47.3µs ± 1%  |  34.1µs ± 1% |  -27.88% | (p=0.008 n=5+5) |
|  AscendRange-20                        |  91.7µs ± 2%  |  54.7µs ± 1% |  -40.36% | (p=0.008 n=5+5) |
|  DescendRange-20                       |   130µs ± 5%  |    72µs ± 1% |  -44.78% | (p=0.008 n=5+5) |
|  AscendGreaterOrEqual-20               |  56.9µs ± 1%  |  42.0µs ± 1% |  -26.19% | (p=0.008 n=5+5) |
|  DescendLessOrEqual-20                 |  94.0µs ± 2%  |  57.4µs ± 1% |  -38.89% | (p=0.008 n=5+5) |
|  DeleteAndRestore/CopyBigFreeList-20   |  5.08ms ± 1%  |  3.60ms ± 0% |  -29.02% | (p=0.016 n=5+4) |
|  DeleteAndRestore/Copy-20              |  5.41ms ± 3%  |  3.66ms ± 0% |  -32.37% | (p=0.008 n=5+5) |
|  DeleteAndRestore/ClearBigFreelist-20  |  2.92ms ± 1%  |  2.16ms ± 1% |  -26.09% | (p=0.008 n=5+5) |
|  DeleteAndRestore/Clear-20             |  3.14ms ± 1%  |  2.24ms ± 1% |  -28.91% | (p=0.008 n=5+5) |

| name | old alloc/op | new alloc/op | delta| |
| ----------- | ----------- | ----------- | ----------- | ----------- |
|  Insert-20                             |   36.0B ± 3%   |  18.4B ± 3% |  -48.89% | (p=0.008 n=5+5) |
|  Seek-20                               |   7.00B ± 0%   |  0.00B      | -100.00% | (p=0.008 n=5+5) |
|  DeleteInsert-20                       |   0.00B        |  0.00B      |     ~    | (all equal) |
|  DeleteInsertCloneOnce-20              |   0.00B        |  0.00B      |     ~    | (all equal) |
|  DeleteInsertCloneEachTime-20          |  2.97kB ± 1%   | 1.95kB ± 4% |  -34.30% | (p=0.008 n=5+5) |
|  Delete-20                             |   0.00B        |  0.00B      |     ~    | (all equal) |
|  Get-20                                |   0.00B        |  0.00B      |     ~    | (all equal) |
|  GetCloneEachTime-20                   |   64.0B ± 0%   |  64.0B ± 0% |     ~    | (all equal) |
|  Ascend-20                             |   0.00B        |  0.00B      |     ~    | (all equal) |
|  Descend-20                            |   0.00B        |  0.00B      |     ~    | (all equal) |
|  AscendRange-20                        |   0.00B        |  0.00B      |     ~    | (all equal) |
|  DescendRange-20                       |   0.00B        |  0.00B      |     ~    | (all equal) |
|  AscendGreaterOrEqual-20               |   0.00B        |  0.00B      |     ~    | (all equal) |
|  DescendLessOrEqual-20                 |   0.00B        |  0.00B      |     ~    | (all equal) |
|  DeleteAndRestore/CopyBigFreeList-20   |   274kB ± 0%   |  142kB ± 0% |  -48.20% | (p=0.008 n=5+5) |
|  DeleteAndRestore/Copy-20              |   876kB ± 0%   |  443kB ± 0% |  -49.36% | (p=0.008 n=5+5) |
|  DeleteAndRestore/ClearBigFreelist-20  |    631B ± 1%   |   510B ± 5% |  -19.06% | (p=0.008 n=5+5) |
|  DeleteAndRestore/Clear-20             |   553kB ± 0%   |  278kB ± 0% |  -49.77% | (p=0.000 n=5+4) |

| name | old allocs/op | new allocs/op | delta |
| ----------- | ----------- | ----------- | ----------- | ----------- |
|  Insert-20                             |    0.00        |   0.00       |    ~    | (all equal) |
|  Seek-20                               |    0.00        |   0.00       |    ~    | (all equal) |
|  DeleteInsert-20                       |    0.00        |   0.00       |    ~    | (all equal) |
|  DeleteInsertCloneOnce-20              |    0.00        |   0.00       |    ~    | (all equal) |
|  DeleteInsertCloneEachTime-20          |    11.0 ± 0%   |   11.0 ± 0%  |    ~    | (all equal) |
|  Delete-20                             |    0.00        |   0.00       |    ~    | (all equal) |
|  Get-20                                |    0.00        |   0.00       |    ~    | (all equal) |
|  GetCloneEachTime-20                   |    3.00 ± 0%   |   3.00 ± 0%  |    ~    | (all equal) |
|  Ascend-20                             |    0.00        |   0.00       |    ~    | (all equal) |
|  Descend-20                            |    0.00        |   0.00       |    ~    | (all equal) |
|  AscendRange-20                        |    0.00        |   0.00       |    ~    | (all equal) |
|  DescendRange-20                       |    0.00        |   0.00       |    ~    | (all equal) |
|  AscendGreaterOrEqual-20               |    0.00        |   0.00       |    ~    | (all equal) |
|  DescendLessOrEqual-20                 |    0.00        |   0.00       |    ~    | (all equal) |
|  DeleteAndRestore/CopyBigFreeList-20   |    12.0 ± 0%   |   14.0 ± 0%  | +16.67% | (p=0.008 n=5+5) |
|  DeleteAndRestore/Copy-20              |   1.18k ± 0%   |  1.13k ± 0%  |  -4.15% | (p=0.008 n=5+5) |
|  DeleteAndRestore/ClearBigFreelist-20  |    1.00 ± 0%   |   1.00 ± 0%  |    ~    | (all equal) |
|  DeleteAndRestore/Clear-20             |   1.07k ± 0%   |  1.02k ± 0%  |  -4.49% | (p=0.008 n=5+5) |

## 原理分析与说明

*细心的人已经发现了端倪,`Seek`的bench结果显示泛型之后的代码的`alloc/op`直接将为了0*

`google/btree`的bench使用的是一个int类型,但是传递的都是一个Item接口.在bench的开头,创建了一个填充了随机数的int数组(同时转换为`btree.Int`,一个`Item`的实现).
```golang
type node struct {
	items    items
	children children
	cow      *copyOnWriteContext
}
type items []Item
type Item interface {
	Less(than Item) bool
}
type children []*node
```
当我们调用一个函数的时候,比如`Seek`实际内部调用`AscendGreaterOrEqual`实际内部去循环`t.root.iterate()`

bench代码模拟了一个大树的随机数据创建,`const benchmarkTreeSize = 10000`个int已经在函数的堆(heap)上了,正常来说,我们调用函数传参都是简单的复制了指向这个int地址的一个指针而已

但是当btree传递数据参数变成`Item`之后,情况就发生了变化,这个时候,传递的`int`地址的指针,变成了一个`Item`的`intertface{}`的指针,这个时候参数就会逃逸到栈上(`escape to heap`)

这种情况其实很常见,比如最常用到的`printf`系列函数,他接收的永远是`interface{}`作为参数,golang的编译器就会认为这类的函数,任意传递的v都会被转换成interface{}之后复制到栈上,再进行处理

这其实很好理解,`v`(aka `Item` in btree)可以是任何类型,传递形参为了保证安全,会**复制**到一个叫做`interface{}`的万能圣杯里,再去传递

```golang
// fmt.Printf 接收任意个interface{}变量,所以在go编译阶段,会逃逸到栈heap上
func Printf(format string, a ...interface{}) (n int, err error) {}

// btree.AscendGreaterOrEqual 接收一个Item变量,实际还是一个实现了Less函数的interface{},同样会被编译器转换成interface{}的指针,这个时候就会逃逸到栈上
func (t *BTree) AscendGreaterOrEqual(pivot Item, iterator ItemIterator) {}

// 泛型实现,在编译阶段就会解析成实际的int类型,此时的pivot就是int,不会发生逃逸
func (t *BTreeG[T]) AscendGreaterOrEqual(pivot T, iterator ItemIteratorG[T]) {}
```

*想要检测是否发生逃逸也很简单,直接使用golang自带的`go build -gcflags="-m" .`*

> golang这些年来做了很多编译的智能推断与优化,实际情况没有这么简单,举一个编译器可以优化的例子:
> 
> 如果函数虽然接收的是一个interface{},但是实际内部只用了一种类型断言的时候,go编译器是不会发生逃逸到栈的处理的
> ```golang
> func IsUIntStr(v interface{}) string {
>   if vi, ok := v.(int); ok && vi > 0 {
>      return "true"
>   }
>   return "false"
> }
> ```
> 在这种情况下,是不会发生额外的复制行为的

回到正题,`btree`接收的类型是`Item`(`interface{}`),当有大量的Item对象调用的时候,需要复制到栈(`heap`)的数据也会很大.

这种情况在bench里面更加明显,`Item`实际内部只是一个`int`的占用空间,但是发生函数调用时,需要生成一个指向这个`int`的完整的`interface{}`,每调用一个参数,增加的指针比实际int的内存占用空间还多的多
> 一个非空的`interface{}` 至少包含
> 
> 1. 一个itab指针,指向一个对象的类型信息
> 2. 一个unsafe.Pointer,指向实际的data地址
> 
> @see: https://github.com/golang/go/blob/master/src/reflect/value.go#L200

使用了 泛型之后,上述的函数在编译阶段就会被解析成:
```golang
func (t *BTreeG[int]) AscendGreaterOrEqual(pivot int, iterator ItemIteratorG[int]) {}
```
这样永远传递的都是指向`int`实际地址的一个确定指针,指挥复制一次指针,在函数的堆内就能完成操作

大量的对象逃逸到栈(heap)的时候,由于heap是随着函数调用随机不连续的调用与复制(copy+alloc),进一步加大gc了的扫描与处理时间,导致最终的性能下降

关于为什么逃逸到堆(heap)之后的go性能会下降,和对应的cpu与alloc相关的详细说明,可以参考下面这些,这里就不再赘述了:
* https://studygolang.com/articles/23711
* https://zhuanlan.zhihu.com/p/404334020

## 引申

从上面的分析可以看到,泛型在处理`interface{}`的问题上提供了一个新的优化思路,在我目前使用的小工具合集的utils里面也有一定的借鉴思路

可以假设有这样一个非常常用的需求: 把一组数组去重,需要同时支持`[]string`,`[]int64`,`[]float64`等等

以前的go代码只有有两种思路:

1. 使用 // go generate 使用模板自动生成N个函数
  但是这样会带来一个问题,不同函数的签名方法名肯定是不一样的,比如`UniqStringSlice`或者是`UniqInt64Slice`,对于调用方来说维护或者查询需要的函数也不简单
2. 使用 `switch .(type)` 断言,全部代码写到一个函数里面去
  但是老的版本,只能使用一个interface{}去接收slice,好处是一个函数走天下,但是需要通过`runtime`+`reflect`的方式去判断具体传入的是怎么样
  `interface{}=>[]interface{}{}=> reflect.Slice(i).Interface()`多种转换,性能必然下降

有了泛型(generics)之后,这类需求就非常容易了:(这也是在我的项目升级之后的utils库里)
```golang
func UniqSlice[T constraints.Ordered](list []T) []T {
	target := make([]T, 0)
	listMap := make(map[T]bool)
	for _, v := range list {
		if _, ok := listMap[v]; !ok {
			listMap[v] = true
			target = append(target, v)
		}
	}
	return target
}
```

## 总结

看起来泛型的引入没有对`google/btree`的代码结构发生大的变化,但是实际测试结果却让性能好了不少,可以预见到很多使用`slice`+`interface{}`的场景,更改为泛型(generics)之后都能带来代码量的减少和一定的性能的提升

后续希望可以看到更多的go代码使用泛型来进行优化,或者给出更多的解决实际问题的思路.



