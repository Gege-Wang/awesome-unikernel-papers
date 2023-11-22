# unikernel design

> \[论文] Flexible operating system internals: the design and implementation of the anykernel and rump kernels
>
> \[论文链接] [https://aaltodoc.aalto.fi/bitstream/handle/123456789/6318/isbn9789526049175.pdf](https://aaltodoc.aalto.fi/bitstream/handle/123456789/6318/isbn9789526049175.pdf)
>
> \[NOTE] 还没看，是一本书



## MirageOS

> \[官方网站] [https://mirage.io/](https://mirage.io/)
>
> \[论文] Unikernels: Rise of the Virtual Library Operating System
>
> \[论文链接] [![Download PDF version of this article](https://queue.acm.org/img/icon\_pdf.png) PDF](https://portal.acm.org/citation.cfm?id=2566628)
>
> \[关键词] cloud computing, Ocaml, Internet&#x20;

1. 提供密封的单一用途设备的unikernel方法，特别适合提供云服务;
2. 使用函数式编程语言(OCaml)对这些技术的完整实现进行评估，表明类型安全的好处不一定会损害性能;
3. 支持OCaml系统编程的库和lan语言扩展。

**面向的应用场景**：多租户数据中心提供面向网络服务的软件。开发时就是面向hypervisor设计的，避免底层硬件不兼容的情况，从而使用管理程序的设备驱动程序。unikernel优点：

* 配置。数据库等作为应用程序的链接库处理，可以使用动态参数库调用或静态参数构建工具来配置，而不是通过大量的shell脚本粘合。
* 代码优化。链接过程是非正常的以应用程序为核心加载库，可进行代码的全局优化，从而消除死代码。代价是需要重新编译。
* 安全。单内核执行尽可能多的编译时优化，静态分析，代价是不能对现有镜像进行写时复制的方法克隆。

## IncludeOS

> \[官方网站] [https://www.includeos.org/](https://www.includeos.org/)
>
> \[论文] IncludeOS: A minimal, resource efficient unikernel for cloud services
>
> \[论文链接] [https://oda.oslomet.no/oda-xmlui/bitstream/handle/10642/3189/1321835.pdf](https://oda.oslomet.no/oda-xmlui/bitstream/handle/10642/3189/1321835.pdf)

1. 极致的资源利用和占用
2. 高效的部署流程。'#include \<OS>' 在链接时可以从预编译的操作系统库中抽取出来形成一个单独的二进制文件，并且写入一个镜像文件。
3. 虚拟平台独立性。可以运行在Openstack中

### 设计原则

1. 零开销原则
2. 静态链接库和GCC工具链。抽取需要的部分是通过链接器实现的，将每个部分编译成.o文件，通过ar生成静态链接库.a文件。所以创建了一个自定义的GCC工具链。没有程序加载器，没有main函数，有service class---service::start。
3. 标准库。newlib可以编译成静态链接库
4. Virtio network driver
5. 模块化、面向对象的网络堆栈。完全模块化的设计。委托作为模块之间的连接
6. 异步I/O和延迟IRQ

## UKL

> \[论文] Unikernel Linux (UKL)
>
> \[论文链接] [https://dspace.mit.edu/bitstream/handle/1721.1/150839/3552326.3587458.pdf?sequence=1\&isAllowed=y](https://dspace.mit.edu/bitstream/handle/1721.1/150839/3552326.3587458.pdf?sequence=1\&isAllowed=y)
>
> \[仓库链接] [https://github.com/unikernelLinux/ukl](https://github.com/unikernelLinux/ukl)

近期，Linux正在进行大规模的重写来减少汇编代码并且将功能转移到C语言这允许UKL转换代码更改本地化到该汇编代码（这句话到底是什么意思呀！！！）。此外，应用程序线程的ABI专用一个寄存器(fs)来指向线程本地存储，而内核线程没有这样的概念，而是专用一个寄存器(gs)来指向处理器特定的内存。为UKL配置的glibc构建过程在构建时首先在UKL特定的目录中搜索目标文件，然后再搜索默认位置。

### 做出的修改

构建二进制程序

**应用程序** ：编译和静态链接 使用两个标志【hermitux使用动态链接和二进制兼容】**链接器** ：内核代码通常没有线程本地存储或C语言构造函数的概念，因此内核的链接器脚本被修改为链接用户空间代码，并确保线程本地存储和C和C的构造函数和析构函数都可以工作。**加载器** ：加载新ELF section和内核。

#### 应用程序和内核代码的转换

使用call/ret，而不是syscall/sysret。syscall指令将返回地址放在RCX寄存器中，但是call把它放在用户堆栈。\


### 产生的问题

应用程序和内核转化的进入和退出代码到底是什么？多种语言之间的互操作性操作系统的静态验证

## Hermitux

> \[官方网站] [https://ssrg-vt.github.io/hermitux/](https://ssrg-vt.github.io/hermitux/)[ ](https://ssrg-vt.github.io/hermitux/)
>
> \[论文] A binary-compatible unikernel
>
> \[论文链接] [https://dl.acm.org/doi/pdf/10.1145/3313808.3313817](https://dl.acm.org/doi/pdf/10.1145/3313808.3313817)

## Nanovms

> \[官方网站] [https://nanovms.com/](https://nanovms.com/)

Deploy unikernels to any cloud in seconds with no devops

## Unikraft

> \[官方网站] [https://unikraft.org/](https://unikraft.org/)
>
> \[论文] Unikraft: fast, specialized unikernels the easy way
>
> \[论文链接] [https://dl.acm.org/doi/pdf/10.1145/3447786.3456248](https://dl.acm.org/doi/pdf/10.1145/3447786.3456248)

Unikernels是OS库的最新设计。它们通常与云环境相关联，由一个与库链接的单一应用程序组成，库提供了足够的功能，可以直接在类似虚拟硬件的界面上运行。&#x20;

Unikernel是根据应用程序量身定制的，所以说，在一些抽象级别上比如说调度 网络栈协议之类进行修改的能够达到最佳性能。例如，一个每秒处理数百万次请求的web服务器可以访问低级的、基于批处理的网络API，而不是标准但速度较慢的套接字API。

&#x20;unikernel以在引导时间、吞吐量和内存消耗等方面提供优异的性能。 然而，提取特征进行改进，这样的改进非常困难和耗时，并且为了将应用程序移植到它们需要大量的工程工作。 当 UNIX 程序从一个 UNIX 平台移植到另一个平台时，遵守该标准可以确保其兼容性.该标准必须阐明操作系统之间互操作性规则。POSIX 涵盖了以下内容：系统接口、命令和实用程序、网络文件访问&#x20;

unikernel有两个主要缺点:它们需要大量的专家工作来构建和提取高绩效;在大多数情况下，这些工作必须为每个目标应用程序重新进行。 •它们通常是非posix兼容的，需要移植应用程序和语言环境。 Unikraft，一个全新的微库操作系统。 (1)完全模块化 (2)公开了一组可组合的、面向性能的api，以使其易于实现.

我们使用现成的应用程序(如nginx、SQLite和Redis)进行了评估，结果显示，在Unikraft上运行它们，与Linux客户相比，性能提高了1.7 -2.7倍。此外，这些应用的Unikraft镜像大约1MB，运行所需的内存不到10MB，在VMM时间的基础上，启动时间大约为1ms(总启动时间为3ms-40ms)。Unikraft是Linux基金会的一个开源项目，可以在www.unikraft.org上找到

&#x20;Unikraft依赖于两个关键原则:内核应该是完全模块化的，以便让单内核能够完全且容易地定制。 就像现在的拖拽式游戏制作平台，给一些角色以及一些道具让你自己选，把一些跑跳的动作也封装起来可选，然后最后合成一个小游戏 在Unikraft中，诸如内存分配器、调度器、网络栈和早期引导代码等操作系统原语都是独立的微型库。内核应该提供关注性能、定义良好的api，这些api可以很容易地选择和组合，以满足应用程序的性能需求。 在Unikraft中，这样的api本身就是微型库，这意味着它们可以很容易地添加到构建中或从构建中删除，而且它们的功能可以通过提供额外的此类微型库来扩展。

简而言之，Unikraft在概念上的创新是为核心操作系统组件定义了一组小的api，当一个组件不需要时，可以很容易地替换它，当性能要求时，可以从同一个组件的多个实现中进行选择。这些api在构建时考虑到了性能(例如，通过设计支持批处理)和最小化(没有不需要的特性)。为了支持广泛的应用，我们移植了musl libc库，并提供了一个系统调用层微库。 因此，在Unikraft上运行一个应用程序就像用它的本地构建系统构建它一样简单，然后将生成的对象文件链接回Unikraft。此外, Unikraft支持许多已经移植的应用程序 (如SQLite, nginx, Redis)，编程语言和运行环境，如C/ c++， Go, Python, Ruby， Web Assembly和Lua，以及许多不同的管理程序/ vmm (QEMU/KVM、Xen、firecracker\[4]和Solo5\[78])。



单地址空间:针对单个应用程序场景，可能有不同的应用程序通过网络通信相互通信。&#x20;

全模块化系统:所有组件，包括操作系统原语、驱动程序、平台代码和库，都应该很容易根据需要添加和删除;即使是api也应该是模块化的。&#x20;

单一保护级别:不应有用户-/内核空间分离，以避免昂贵的处理器转换 上下文切换，这是非常消耗系统资源的。这并不妨碍以合理的成本实现分区(例如，微型库)&#x20;

静态链接:启用编译器特性，例如Dead代码消除(DCE)和链接时间优化(LTO)，自动删除不需要的代码，更加轻量级提高性能&#x20;

POSIX支持:为了支持现有的或遗留的应用程序和编程语言，同时仍然允许在该API下专门化。&#x20;

平台抽象:云环境连接起来的 ，底层可能有不同的VMM 为一系列不同的管理程序/ vmm无缝生成镜像。

## Arceos

> \[仓库链接] [https://github.com/rcore-os/arceos](https://github.com/rcore-os/arceos)

参照 unikraft 的组件化操作系统，采用 rust 语言编写。

##
