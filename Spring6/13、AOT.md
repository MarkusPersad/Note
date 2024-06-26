# 提前编译：`AOT`

![image-20221218154841001](/home/markus/Documents/GitHub/Note/images/2024-4-26-16-43.png)

## `AOT`概述

### `JIT`与`AOT`的区别

`JIT`和`AOT` 这个名词是指两种不同的编译方式，这两种编译方式的主要区别在于是否在“运行时”进行编译

#### `JIT`， `Just-in-time`,动态(即时)编译，边运行边编译

在程序运行时，根据算法计算出热点代码，然后进行` JIT` 实时编译，这种方式吞吐量高，有运行时性能加成，可以跑得更快，并可以做到动态生成代码等，但是相对启动速度较慢，并需要一定时间和调用频率才能触发` JIT` 的分层机制。`JIT` 缺点就是编译需要占用运行时资源，会导致进程卡顿。

#### `AOT`，`Ahead Of Time`，指运行前编译，预先编译

`AOT `编译能直接将源代码转化为机器码，内存占用低，启动速度快，可以无需` runtime `运行，直接将 `runtime `静态链接至最终的程序中，但是无运行时性能加成，不能根据程序运行情况做进一步的优化，`AOT` 缺点就是在程序运行前编译会使程序安装的时间增加。                                                           

**简单来讲：**`JIT`即时编译指的是在程序的运行过程中，将字节码转换为可在硬件上直接运行的机器码，并部署至托管环境中的过程。而 `AOT `编译指的则是，在程序运行之前，便将字节码转换为机器码的过程。

在程序运行前编译，可以避免在运行时的编译性能消耗和内存消耗
可以在程序运行初期就达到最高性能，程序启动速度快
运行产物只有机器码，打包体积小

**`AOT`的缺点**

由于是静态提前编译，不能根据硬件情况或程序运行情况择优选择机器指令序列，理论峰值性能不如`JIT`
没有动态能力，同一份产物不能跨平台运行

第一种即时编译 (`JIT`) 是默认模式，`Java Hotspot `虚拟机使用它在运行时将字节码转换为机器码。后者提前编译 (`AOT`)由新颖的 `GraalVM` 编译器支持，并允许在构建时将字节码直接静态编译为机器码。

现在正处于云原生，降本增效的时代，`Java` 相比于` Go`、`Rust` 等其他编程语言非常大的弊端就是启动编译和启动进程非常慢，这对于根据实时计算资源，弹性扩缩容的云原生技术相冲突，`Spring6 `借助` AOT` 技术在运行时内存占用低，启动速度快，逐渐的来满足 `Java` 在云原生时代的需求，对于大规模使用 Java 应用的商业公司可以考虑尽早调研使用 JDK17，通过云原生技术为公司实现降本增效。