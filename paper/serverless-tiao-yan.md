# Serverless 调研

建立和运行应用程序而不需要服务器管理，细粒度的部署模型，应用包装成一个或者多个函数，上传到平台执行 伸缩计费，响应当前的确切需求。在使用时提供后端服务的方法.serverless提供程序允许用户编写和部署代码，无需担心底层基础设施。尽管名称是serverless，但是仍然使用物理服务器，但是开发人员不需要了解他们。也就是说serverless是一个部署平台，那么需要的东西时**代码执行，弹性伸缩，计费** 都是平台去完成，用函数构建应用，解耦计算和存储，事件驱动，为函数实际运行来计费。一种视角：没有服务器，代码始终在运行。供应商需要管理服务器（虚拟机和容器）

* 存储和计算解耦，并且时独立配置和定价的，存储有单独的云提供服务，计算是无状态的。
* 执行代码而不管理资源分配，云提供资源来执行任务代码？？？什么意思，这和Paas不一样吗，提供了运行时？？
* 按照资源使用比例支付，而不是按照资源分配比例支付

我们根据这三个指标来判断应用是否是serverless架构![](https://uupa9ccddu.feishu.cn/space/api/box/stream/download/asynccode/?code=YWQ2NWFiNGZiYzY2NjVhOWIyNzVhNDY5ODlkMTU2YjJfQVFETG82eVBRQ2JGdHdjdFU3U093cUJjVXQzMU1UZ1ZfVG9rZW46VndFUmJ4SWJVb1hYV014YVlSUGN3cml5bjVjXzE3MDA2MjMwMTg6MTcwMDYyNjYxOF9WNA)

**函数运行在Faas平台上，使用了Baas的能力**比如说我们要运行python程序，我们需要安装环境，我们使用hadoop我们需要先安装环境，但是有了serverless我们就直接写代码，只要代码正确就能跑。serverless的运维是不需要消费者去做的，而是平台去做，开发人员专注于业务逻辑就行。serverless不完全是Faas,满足上面三条就是serverless。对于我一直疑惑的问题，serverless和Paas的区别：Paas的范畴要大的多，所有与应用程序相关的开发工具和中间件数据库管理以及操作系统都能算，但是serverless小的多，对开发者也更加透明，在开发者看来就基本认为是一个执行函数的平台。我们立刻比较一下，之前的服务为什么需要担心底层设施底层设施指的是什么？？？Iaas:通俗的理解成基础设施，比如提供虚拟机Paas:提供平台，这里\*\***AWS云架构战略副总裁Adrian Cockcroft曾经针对两者的界定给出了一个简单的方法：“如果你的PaaS能够有效地在20毫秒内启动实例并运行半秒,那么就可以称之为Serverless”。**\*\* \
一个重要的概念：没有服务器，替换了点击代理服务器，这在之前可能是一个物理服务器或者容器中的应用程序，在serverless中这似乎不应该有一个应用程序在持续运行。Faas是无状态的，函数调用中创建的所有中间状态和环境状态都不会影响之后的任何一次调用。这里的状态包括内存数据和本地磁盘存储数据。执行时长：Faas的执行时间通常受限，AWS Lambda函数执行最长不能超过五分钟。\


#### 轻量级隔离

这是一种以弱化隔离性为代价的解决冷启动开销的方案。SOCK(\[15])设计了新型的容器模型，阉割了Docker中的部分功能，如使用NAT转发替换网络隔离，使用Mount-Bind替换AUFS的文件系统隔离等；SAND(\[10])认为，同Application的Function之间不需要强大的隔离，因此设计了一种两层的隔离方案，使用进程隔离Function，使用容器来隔离Application，并通过fork来创建新实例；FAASM(\[16])则直接将实例放在同一进程地址空间中，使用线程来驱动实例的执行，并通过WebAssembly机制来确保内存安全！

![](https://uupa9ccddu.feishu.cn/space/api/box/stream/download/asynccode/?code=Y2Q3MjEzNmM1YjE4NDY0NzNjZWIxYmQ0NzEzMDQyNDFfaVAwMHNPTXV5TXo0RFFRZENmeFB1a1Z6dWU0Z3kzQVNfVG9rZW46VE9wbWJyd2Izb1oxSVl4eWpBNGNQcjRUbmtnXzE3MDA2MjMwMTg6MTcwMDYyNjYxOF9WNA)

图8. SADN(L)、FAASM(R)的隔离方案\


### Faas

让我们想一些云计算出现之前的场景。我们有一个本地的网络堆栈，我们有本地的硬件，这将是我们所有的计算，网络和存储资源。虚拟化层操作系统 运行时：这是操作系统和应用程序之间的粘合剂应用程序：\
Iaas:让我们说的更加通俗易懂一些，这就是硬件层和虚拟化层，意思是开发人员不需要维护这些层，我们买了这些服务，所以我们不需要为它而浪费时间了P aa s:我们到了运行时和容器级别也不需要自己来管理了Faas，这个是应用程序层面的，faas抽象了应用程序\


### docker and containerd

k8s and containerd cli tools \_ ctr \&nerdctl& crictlk8s来编排docker，当时k8s是和docker 强耦合的并且不支持其它的容器解决方案。oci包括 imagespec和 runtimespec和distribute spec。任何遵循oci规范的容器解决方案都可以通过cri来使用k8s进行编排，但是docker是不支持cri的，所以后来k8s给他走了个后门，使用了docker-shim。，其他的容器解决方案不需要使用docker-shim。后来k8s直接删除了对docker的支持【docker不仅仅是一个容器运行时，docker包含一组工具，包括docker cli , api , build tool , volums, auth, security, runc（容器运行时），containerd(这就是那个守护进程)】现在将containerd从docker中分离出来现在用于调试的是ctr, crictlnertdctl是一个类似于docker命令的工具总结： ctr和nerdctl是containerd社区的，而crictl是k8s社区用来支持不同容器运行时的后来k8s变得非常出色，他需要支持其它的容器，那么她引进了新的 容器运行时接口——CRI。这个接口允许其他的所有的符合OCI标准的供应商使用k8s进行编排

![](https://uupa9ccddu.feishu.cn/space/api/box/stream/download/asynccode/?code=NWEzNjM3OTFjMjM0MDc3MjM0MDQwMzA0NThhYjI2OTFfYU9Ddlo1ZXI2eGlRdUJ0RTRhaThDMHBiS3RUNUU4MWxfVG9rZW46VU0xVWJoUVlqb1pMZDZ4aDN3WWNDNnJEbjYxXzE3MDA2MjMwMTg6MTcwMDYyNjYxOF9WNA)\
\


### youtube的kata视频

可信计算。内存加密等等每个pod有自己的可信计算实例每个pod的内存和CPU是加密的每个pod的软件栈是验证过的容器镜像被加密了，并且被主机保护无缝 ，应用无需修改



## serverless 实验

what?将事件驱动的函数和微服务部署到k8s，将代码和现有二进制文件打包在docker镜像中。获得自动缩放和高度可扩展性端点。

![](https://uupa9ccddu.feishu.cn/space/api/box/stream/download/asynccode/?code=MTNhMDYyZTgzN2Y3YjNiMjlkNWJlYjJmYTE0OTVlMDhfa2ZyQUFMMkpyOFlMZzBiZXJDc2IxR2tRRWNXYWtoYjRfVG9rZW46VGNyWWJrMGg2b1lUZUh4OVFHa2M4czJDbmhiXzE3MDA2MjM5OTk6MTcwMDYyNzU5OV9WNA)\
\
command line interfacefrequently asked question

### 关于openfaasd

* 静态 golang 包
* 使用和 openfaas 相同的核心组件和生态
* 使用 containerd 作为运行时和 CNI 作为网络插件
* 支持多架构，包括 x86 和 ARM。
* 可以通过 docker-compose.yaml 文件跑任何有状态的容器。

faasd是openfaas的单节点部署，不支持多副本，集群，HA和自动缩放。openfaas安装和部署\[自动安装]\<repo 仓库中有一个/hack/install.sh脚本>\[手动安装]\<https://github.com/openfaas/faasd/blob/master/docs/DEV.md>

### 关于multipass

### 关于faas-cli

### 关于github Action

工作流程（一个或多个作业），由yaml文件定义，并在存储库中的事件触发时运行存储库里有工作流和触发事件，到有触发事件就开启对应的工作流。

### reference

\[中文:openfaas介绍]\<https://atbug.com/openfaas-case-study-zh/>\[官方文档：部署multipass方法]\<https://github.com/openfaas/faasd/blob/master/docs/MULTIPASS.md>\[openfaas roadmap]\<https://github.com/openfaas/faasd/blob/master/docs/ROADMAP.md>

### Reference

\<https://blog.abreaking.com/article/161>

\<https://amio.github.io/serverless-zhcn/>

##
