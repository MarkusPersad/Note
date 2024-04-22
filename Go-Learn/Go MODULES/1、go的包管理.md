# `GoLang`的包管理

自`go` `Version 1.11`后，`go`推出了一个好用的包管理模式，即`go modules`。在此之前，`go`的项目结构只能是`GOPATH`模式。

设置一个环境变量`GOPATH`，值是一个文件路径例如`$HOME/go`这个文件夹下包含三个子目录，分别是`bin`，`src`，`pkg`。

- `bin`目录下放的是`go`的一些构建工具，类似`glops`等。
- `src`目录下是我们程序的源代码
- `pkg`下的是我们所依赖的一些`package`包括本地的和远程的，本地的包还需要通过设置`GOPATH`来让`import`可以读取

这个模式包管理的缺点显而易见

- 设置`GOPATH`对`go`项目太麻烦了，每次引用本地包都得设置一下`GOPATH`,`GoLand`还好，`VScode`就比较难受
- 多个项目对同一个包的依赖是不同版本，这个也不好弄
- `go`项目被限制在`src`目录下

在`go`的模块系统推出后，可以最大程度地改善以上状况。`go module`与`Rust(Cargo)、Java(Maven.Gradle)、JavaScript/TyprScript(npm/pnpm/cnpm)`的管理模式高度相似通过一个项目文件对依赖进行统一的版本管理，各个项目间版本互不干扰。一个`package`下有一个`go.mod`文件格式如下

```mod
module packageName (eg: local.com/package/v2)
go 1.22.2 #项目构建依赖的go版本

#本地包导入
replace package1 => /path/to/package（所在的文件夹，该文件下，也应该有一个go.mod文件）

require(
	package1 v1.0.0
	package2 vx.x.x
)
#远程包即通过`go get`命令下载的包，不用设置replace,直接可以在require下引入
```

`go.mod`文件可以通过`go mod init packageName`命令生成，也可以自己新建

启用`go Module`模式，需要保证环境变量`GO111MODULE`的值是`auto/on`。

`GOPATH`在`go module`模式下单纯地变为了下载包的保存路径和`go`构建工具的保存路径和`Rust`的`CARGO_HOME`一样