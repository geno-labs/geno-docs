# 开发环境

## 安装Rust编译环境
Geno智能合约是 `Rust` 语言，所以合约编译过程依赖 `Rust` 语言编译器`rustc`及代码组织管理工具`cargo`，且均要求版本号 `1.67.0`。可以参考官网[安装 Rust](https://www.rust-lang.org/zh-CN/tools/install)。下面简单的做一下安装演示，：

*  对于Linux以及Mac系统，可以直接终端中运行以下命令；
```
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
* 对于Windows用户，要使用 Rust，请下载安装器，然后运行该程序并遵循屏幕上的指示。
  - [32位系统下载](https://static.rust-lang.org/rustup/dist/i686-pc-windows-msvc/rustup-init.exe)
  - [64位系统下载](https://static.rust-lang.org/rustup/dist/x86_64-pc-windows-msvc/rustup-init.exe)

> 💡**注意**  
> *受限于网络情况，安装过程可能耗时较长或者失败。*  
> *推荐切换Rustup官方镜像源为国内镜像源，请参考 [Rustup 镜像](https://mirrors.tuna.tsinghua.edu.cn/help/rustup/) 更换镜像源。*  

安装完毕后，可以测试是否安装正确：
```
rustc --version
cargo --version
```
如果安装后在终端尝试执行 `rustc --version` 失败，可以检测`~/.cargo/bin 目录`,您可以在这里找到包括 rustc、cargo 和 rustup 在内的 Rust 工具链。然后将该目录加入 `PATH`环境变量中， 即可解决问题；

然后安装合约组件：

```
rustup target add wasm32-unknown-unknown
```

## 安装合约依赖包

创建文件夹`contract`,将`geno-rust-smart-contracts`文件夹移动到`contract`中。

## 合约优化工具

下载合约优化工具[合约优化工具](https://github.com/WebAssembly/wabt/releases)，根据自己的操作系统下载对应的工具；
然后将工具添加到环境变量中。

测试`wasm-strip`工具：
```
 wasm-strip --version
```
