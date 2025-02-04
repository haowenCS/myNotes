
8 小时转职 Golang 工程师(如果你想低成本学习 Go 语言) https://www.v2ex.com/t/876104
```console
视频链接： https://www.bilibili.com/video/BV1gf4y1r79E 文章链接: https://www.yuque.com/aceld/mo95lb/dsk886
```

go mod tidy 可能会修改指定的依赖版本号？ https://www.v2ex.com/t/854360

女同事问了一个 golang 的技术问题，没答上来，溴大了 https://www.v2ex.com/t/773635
```console
两个 goroutine：go1,go2
go1 从 socket 读，读到的数据写到 ch1
go2 从 ch2 读，读到的数据写到 socket
以此来实现全双工通信

问题：ch1 关闭之后，go1 不知道，一写就崩了，怎么避免？
```
- > ch1 由 go1 关闭，也就是永远由写的一方关闭 channel
- > go1 来关闭啊，怎么可能由消费者去关闭通道 <br> go2 用 for range 去读，也能感知到关闭

由浅到深，入门 Go 语言 Map 实现原理 https://www.v2ex.com/t/737613
```console
文章链接: 由浅到深，入门 Go 语言 Map 实现原理 https://mp.weixin.qq.com/s/sooEw_-Sb_sEII429M8Uiw
```

golang context 的相关使用 https://www.v2ex.com/t/728389
```console
如题，之前的项目中没有使用 context，高并发的场景下，日志都难以定位问题，
于是准备在所有逻辑函数中加上 context （工作量有点大），并为每次请求带上 id 标识；

但是除此之外就没有其他作用了，就有点怀疑是否有必要；
有没有大佬介绍下实际中的其他使用场景和方式？（ With 系列目前也没有发现有什么使用场景...）
```
- > At Google, we require that Go programmers pass a Context parameter as the first argument to every function on the call path between incoming and outgoing requests. <br> 建议是所有的函数的第一个都带上。作用有几个：cancel, timeout, trace

golang 中的切片地址 https://www.v2ex.com/t/728365

求推荐个 demo 展示 go 相较于 Python 性能优势的 https://www.v2ex.com/t/726024
- > 遍历 100000000 次  go: 0.058280 seconds  py: 3.965864 seconds  go 只消耗 1/68 倍 py 的运行时间  孰优孰劣，一目了然
```go
package main

import (
	"fmt"
	"time"
)

func main() {
	st := time.Now()
	n := 100000000
	for i := 0; i < n; i++  {
	}
	fmt.Println(fmt.Sprintf("%f seconds", float32(time.Now().Sub(st).Microseconds()) / 1000000))
	// 0.058280 seconds
}
```
```py
from datetime import datetime
st = datetime.now()
n = 100000000
_ = [i for i in range(n)]
ed = datetime.now()
d = ed - st
print('%f seconds' % (d.seconds + d.microseconds / 1e6))
# 3.965864 seconds
```
- > https://www.bilibili.com/video/BV13J411m7un
- > 用 Rust 跑了一下上边的循环，不讲武德，哈哈  time cost: 0 ms
- > https://benchmarksgame-team.pages.debian.net/benchmarksgame/fastest/go-python3.html  看这个对各个语言的 benchmark 吧

请教一个 go 的函数问题 https://www.v2ex.com/t/717494
- > C++你把引用捕获改为赋值捕获就是 100 了
  >> 真是大神，佩服
- > go 中关于闭包的一些实现过程可以看这个， [go 闭包的实现](https://tiancaiamao.gitbooks.io/go-internals/content/zh/03.6.html) <br> 同时，把你的代码复制到，https://godbolt.org/ , 选择 go 语言，再选择 386gc tip，对照汇编结果来理解。
  >> 【[:star:][`*`]】 Compiler Explorer https://github.com/compiler-explorer/compiler-explorer || https://godbolt.org/
- > 好吧，c++ capture 换成 = 号后我又有一个疑问，这个值是什么时候复制的，是在调 Set 的时候还是调 f 的时候？？？
  >> 两步都有复制。调用 Set 的时候，复制了 100 到一个地方存下来。调用 f 的时候，再把存下来的值复制给 s._i
  >>
  >> 在 lambda 表达式的内外，参数 i 的意义是不一样的。在表达式的内部：1.引用捕获的时候，它是参数 i 的引用，调用 f 的时候，参数 i 已经没有了，所以最终得到的结果是随机的； 2.赋值捕获的时候，它是参数 i 的复制
- > 闭包，Set 执行完后返回的是一个函数，再执行把指针传进去，那肯定把 ss 的值给改了

Go 编写的跑在 x86 裸机上的 unikernel https://www.v2ex.com/t/713999
```console
github 地址 https://github.com/icexin/eggos

Go 的 runtime 提供了一些基本的操作系统抽象，goroutine 对应进程，channel 对应进程间通信，另外 go 有自己的
虚拟内存管理，所以萌生了在裸机上运行 Go 程序的想法。
```

[7 天用 Go 从零实现系列] 第四期 RPC 框架 GeeRPC 已经完结 https://www.v2ex.com/t/713101
- 7 days golang apps from scratch https://github.com/geektutu/7days-golang

看到好多人吐槽 golang 的错误处理，但我用的很爽啊 https://www.v2ex.com/t/706008

golang 里面全局匿名变量的作用是啥？ https://www.v2ex.com/t/705928

[视频版]-Golang 深入理解 GMP https://www.v2ex.com/t/696070
- > 视频版：典藏版-Golang深入理解GMP https://www.bilibili.com/video/BV1jT4y1j7Cd
- > 文字版：[典藏版]Golang调度器GMP原理与调度全分析 https://www.jianshu.com/p/fa696563c38a
- > 关于作者：
  * > 原创书籍 gitbook: https://legacy.gitbook.com/@aceld
- 其他：
  * [典藏版]Golang调度器GMP原理与调度全分析 https://www.bilibili.com/read/cv5098443 【这个是在作者B站主页找到的，应该和简书版一样】

Go 中为啥喜欢这样创建变量 https://www.v2ex.com/t/695208

golang 中使用 goto 跳出 if 语句 https://www.v2ex.com/t/692272

为什么 Go 语言可以交叉编译，打包出目标平台二进制， Java 不行？ https://www.v2ex.com/t/690116

我的机器上 go get 指令一直报握手错误。从下午查到晚上还不知道啥问题 https://www.v2ex.com/t/687790

Dig101-Go 之深入理解 mutex https://www.v2ex.com/t/687253

关于 golang 除法运算符/的疑惑 https://www.v2ex.com/t/685694

Golang 泛型他来了！ https://www.v2ex.com/t/682238
```console
https://blog.golang.org/generics-next-step

官方发布了几份草案，其中 go2 的泛型可以前往 https://go2goplay.golang.org 体验
```

有个 go 的问题，求解答 https://www.v2ex.com/t/680907

go 单例模式 https://www.v2ex.com/t/678414

golang 面试题之 为什么这种更快呢? https://www.v2ex.com/t/677996

go 发生死锁的问题 https://www.v2ex.com/t/677881

[请教] 运行一段时间后报： goroutine 10 [chan send, 20 minutes] https://www.v2ex.com/t/677833

初学 GO，怎么判断一个变量是否未初始化/是否为空 https://www.v2ex.com/t/674053
- > 看 go 语言之旅 <br> 科学的地址 https://go-tour-zh.appspot.com/ <br> 不科学的地址 http://tour.studygolang.com/welcome/1

为什么在 Go 数组(array)被设计成值，而不跟 C/C++或 Java 一样，设计为一个引用？ https://www.v2ex.com/t/673135

Go Channel 学习的一个疑问，请大佬指教 https://www.v2ex.com/t/672517
```console
为什么这个输出结果一会是 0，1，2，3，一会是 0，1，2，3，4
按道理来说，我在主进程一直阻塞了，应该等 goroutine 全部打印完才对呀，百思不得其解
```
```go
func main() {
	boolChannel()
}

func boolChannel()  {
	ch1 := make(chan int)
	ch2 := make(chan bool)

	go func() {
		for i:=0;i<5;i++ {
			ch1 <- i
		}
		ch2 <- true	//标识位写入 true
	}()

	go func() {
		for x:= range ch1{
			fmt.Println(x)
		}
	}()

	<-ch2 //阻塞标识位
}
```
- > ch2 <- true 改成 close(ch1) <br> ch2 <- true 移到第二个 for 后面
- > 4 进入 ch1 后，true 进入 ch2，
  <br> 情况 1：主程序先得到 ch2，第二个 func 还没来得及得到 ch1 或者没来得及打印， 程序就退出 输出 0,1,2,3
  <br> 情况 2：第二个 func 得到 ch1 并且打印，主程序还没来得及得到 ch2,输出 0,1,2,3,4

go 语言对接 c 语言的问题 https://www.v2ex.com/t/667279

[典藏版]Golang 调度器 GMP 原理与调度全分析 https://www.v2ex.com/t/651092
- > [典藏版]Golang调度器GMP原理与调度全分析 https://www.jianshu.com/p/fa696563c38a

采用 gomod 方式后 gopath 下的 bin 如何处理？ https://www.v2ex.com/t/651007

Go 代码编译为 C 代码 https://www.v2ex.com/t/648000
> A toy programming language base on Golang and compile to C, no name yet https://github.com/kitech/cygo

[7 天用 Go 从零实现系列] 第二期分布式缓存 GeeCache 已经完结 https://www.v2ex.com/t/645362
> 7 days golang apps from scratch (web framework Gee, distributed cache GeeCache, etc) 7天用Go动手写系列[从零实现] https://geektutu.com/post/gee.html || https://github.com/geektutu/7days-golang

Go 101 v1.13.m (v.1.14-pre) 电子书发布 https://www.v2ex.com/t/635029
> Go语言101 : 一本侧重于Go语言语法和语义的编程解释和指导书 https://github.com/golang101/golang101
>> Go语言101 https://gfw.go101.org/article/101.html

GO 切片问题求教 https://www.v2ex.com/t/606165

:u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272::u5272:
