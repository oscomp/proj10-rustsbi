# proj10-rustsbi
### 项目描述

RISC-V指令集的SBI标准规定了类Unix平台下，操作系统运行环境的规范。这个规范拥有多种实现，RustSBI是它的一种实现。

RISC-V架构中，存在着定义于操作系统之下的运行环境。这个运行环境不仅将引导启动RISC-V下的操作系统， 还将常驻后台，为操作系统提供一系列二进制接口，以便其获取和操作硬件信息。 RISC-V给出了此类环境和二进制接口的规范，称为“操作系统二进制接口”，即“SBI”。

SBI的实现是在M模式下运行的特定于平台的固件，它将管理S、U等特权上的程序或通用的操作系统。

SBI对处理器和板级资源进行了最初的屏蔽和抽象，为操作系统提供了最原始的支持机制，操作系统进而实现更高级的策略。本项目要求实现以下三种机制: 
*  在SBI层实现对指令集的扩展(ISA Extension emulation).
*  在SBI层实现OS固件防篡改机制.
*  开发功能，将SBI无法处理的ecall系统调用委托给S模式处理.

### 所属赛道

2021全国大学生操作系统比赛的“OS功能设计”赛道

### 参赛要求

* 以小组为单位参赛，最多三人一个小组，且小组成员是来自同一所高校的本科生（2021年春季学期或之后本科毕业的大一~大四的学生）
* 如学生参加了多个项目，参赛学生选择一个自己参加的项目参与评奖
* 请遵循“2021全国大学生操作系统比赛”的章程和技术方案要求

### 项目导师

**曹子龙(caesar)**

* github
* email caozilong@allwinnertech.com

### 难度

中等

### 特征

* 适配 RISC-V SBI 规范 v0.2
* 对类 Unix 操作系统有很好支持
* 完全使用 Rust 语言实现
* 具备 OpenSBI 的大部分功能
* 支持 QEMU 仿真器（RISC-V 特权级版本 v1.11）
* 向下兼容 RISC-V 特权级版本 v1.9
* 支持 Kendryte K210（含MMU和S模式）

### 文档

[项目文档](https://github.com/13824125580/oscom/blob/master/allwinner_sbi.docx)

### 平台实现的注意事项
* S模式的ecall指令需要delegate到M模式处理.
* S模式的非法指令异常需要delegate到M模式处理.
* 运行于M模式的SBI按照 "截获-处理-分派-返回" 几个步骤处理S模式进来的请求.

### License
* [The MIT License](https://opensource.org/licenses/MIT)
* [MulanPSL - 2.0](https://opensource.org/licenses/MulanPSL-2.0)


## 预期目标

### 注意：下面的内容是建议内容，不要求必须全部完成。选择本项目的同学也可与导师联系，提出自己的新想法，如导师认可，可加入预期目标

### 第一题
* 开发SBI固件，实现对S模式指令集的扩展。应用场景包括.
1. 处理器可能没有实现某条指令对应的ISA扩展，但方案预编译的软件包中可能包含对相应ISA指令集的使用。 在CPU算力足够的情况下，可以利用RISCV处理器的异常委托机制，使S模式的非法指令异常陷入进M模式，由SBI对相应指令进行模拟实现，再将结果返回给S模式继续处理，整个过程S模式不感知。       
2. S模式的OS扩展自定义指令来简化某些常用操作，通过内嵌汇编的方式调用扩展指令，实现对指定操作的定义.

### 第二题
* 编写SBI固件扩展，实现对S模式固件的完整性检测,当检测到S模式运行的程序被修改，则CPU在M模式stop,不返回S模式.
* 应用场景：安全场景，要求OS系统在运行中未被篡改

可以在此基础上适当扩展，比如，在完整性检测的基础上，加入数字签名的身份验证机制，既保证固件未被篡改，又保证固件的来源合法。
### 第三题
* 编写SBI固件，支持系统调用重定向扩展，当S模式通过ecall指请求系统服务的时候，要求：
1. 对于SBI支持的系统调用，由SBI实现处理，比如SBI legcay的服务等等
2. 对于SBI没有实现或者无法实现的系统调用，要求SBI将其delegate到S模式，由操作系统处理，不能返回不支持。

* 此项功能可以应用于S模式提供了一组系统调用，并且希望S模式的功能组件可以通过ecall指令申请这些系统调用服务的场合。