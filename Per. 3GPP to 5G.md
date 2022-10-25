## 一、3GPP to 5G

1. 3GPP对5G定义了空中接口，启动整个移动系统的所有协议和网络接口，呼叫和会话控制，移动性管理，服务供应。
2. 3GPP分为几个阶段，例如Release 15规定了5G的第一个阶段，该阶段引入了一个新的无线电传输技术。这一代一代都是为了所有行业和关键行应用做好准备（如自动驾驶）。
3. 为了提升用户体验，引入了一些列的专用技术：网络功能虚拟化和切片，边缘计算，非-地面网络和卫星网络，提供无处不在的覆盖。



### 1. 5G的目标：宗旨-> 提供更好的服务

1. 什么叫更好的服务？

- 增强型移动宽带eMBB（上行和下行速度超级快，甚至可以提供航天服务) 

- 关键通信（CC）和超可靠和低延迟通信（主要是边缘计算能力达到的）

- 大规模物联网（支持高流量密度）

- 灵活的网络操作（网络切片，网络能力暴露，可扩展性，安全性）



### 2. 整体架构

组成结构：

- 用户设备（UE），基本由移动站和USIM组成

- 无线接入网络（NG-RAN）：无线电发射机（gNB），无线电接口（NR-Uu），gNB可分为一个gNB中央单元（gNB-CU）和多个分布式单元（gNB-DU），通过F1接口链接。

- 核心网络（5GC）：移动管理功能（AMF）和用户平台功能（UPF）。

  UPF处理用户数据，AMF访问UE和R的接入和移动管理功能。



我的理解是这样：

- AMF是核心网的最底层，负责最基本的链接，相当于传输层。

- UPF是核心网的最高层，负责和最顶层的服务，处理用户的数据。相当于应用层接口。

- SMF是与负责用户面的会话，建立，修改，释放。相当于会话层

而AMF和SMF的中间贯穿着很多服务，比如AUSF身份验证服务器，PCF的策略控制（负责控制用户流量）等等。

<img src="/Users/apple/Library/Application Support/typora-user-images/image-20221014215123131.png" alt="image-20221014215123131" style="zoom:50%;" />

Notes：重新补充一下基于服务的接口SBI做融合计费服务。



### 3. 5G协议栈

1. 协议让UE接受各种网络服务。

   例如UE去接受SMF服务，也就是到会话层的服务。

   <img src="/Users/apple/Library/Application Support/typora-user-images/image-20221014215100473.png" alt="image-20221014215100473" style="zoom:50%;" />

注意，原始的三层是L1物理层，L2数据链路层，IP网络层，SCTP流量控制……

NAS-MM去控制AMF连接的注册，并且NAS-SM完成会话层的功能。



### 4. 5G接口

1. 第一层，就是网络到UE的下行链路，无线电技术使用带有循环前缀CP的OFDM，类似于LTE。对于从UE到网络的上行链路，使用OFDM和FDT-s-OFDM（具有傅立叶变换预编吗的OFDM）。



​      对于物理层的频段选择。多频段设计，以便能在每个国家或者地区的频率上部署。

​      对于地面来说，确定了三个频段的范围：

- 高达 1 GHz：凭借其更好的传播特性，该系列旨在覆盖大面积区域，通常用于农村部署。一个载波的最大带宽为 100 MHz。
- 从 1 到 6 GHz：此中间范围适用于城市或郊区环境中的 5G 部署。在这里，最大带宽也是 100 MHz。
- 高于 6 GHz：由于其传播较差但对用户的带宽较高（最大带宽为 400 MHz），此范围适用于密集的城市环境（“热点”类型的覆盖）。



### 5. 5G的独立架构和非独立架构

1. 非独立架构是5G无限点是新的，并且NR（new radio）也是新的，但是核心网的架构是LTE和EPC的，也就是4G的。支持4G的服务，但是享受5G的容量。被称为NSA架构

   4G的服务是啥子？ MME/S-GW服务，不是AMF/URF服务哦。

   并且4G的eNB是主节点，但是5G的en-gNB是辅助节点。

   

   <img src="/Users/apple/Library/Application Support/typora-user-images/image-20221014213921822.png" alt="image-20221014213921822" style="zoom:50%;" />

2. 独立架构是只UE，AN，核心网都是5G全新的，支持5G版本的服务。

<img src="/Users/apple/Library/Application Support/typora-user-images/image-20221014213959755.png" alt="image-20221014213959755" style="zoom:50%;" />



### 6. 5G的一些特点

1. 到现在我的直接感觉就是：（1）5G的AN更强，也就是物理接入收到导致接入量变大，延迟小。（2）是5G的服务更多，耦合性更低，变动性更好。

   

2. 最新的技术

   网络切片：并不是一个核心网，而是使用不同的CN核心网。每个核心网专门提供给特定的服务，或者一组给定的订户。比如，一个切片可以支持普通运营商的订户，另一个可以专用于支持虚拟运营商的订户。另一个可以处理特定的服务等等。

   

   网络功能的虚拟化：每个网络功能（我猜测也就是虚拟机）是部署软件在上面的。因为是虚拟机，所以可以灵活的安装和接触软件，使之提供新的服务。

   

   边缘计算：也就是让UE和有计算能力的设备更加接近，这样会减少时间，比如：虚拟现实的高同步，自动驾驶对于网络的高要求，都要通过大量主服务器和从服务器的计算和同步去完成。



### 7. 3GPP对于5G网络提出了多方面的标准，也就是从方案到实现方式的完整版

为了帮助您找到更多信息，例如关于 5GS 的动态行为（程序等）或 5G 的某些特定方面，此处列出了一些关键的 TR 和 TS：

- [TS 22.261](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=3107)，“5G 系统的服务要求”。
- [TS 23.501](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=3144)，“5G 系统 (5GS) 的系统架构”
- [TS 23.502](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=3145) “5G系统（5GS）程序
- [TS 32.240](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=1896) “充电管理；充电架构和原则”。
- [TS 24.501](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=3370) “用于 5G 系统 (5GS) 的非接入层 (NAS) 协议；第 3 阶段”
- [TS 38.300](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=3191) “NR；NR 和 NG-RAN 总体描述；第 2 阶段”

### 参考规格：

1. [TS 22.278](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=641)，“演进分组系统 (EPS) 的服务要求”。
2. [TS 22.011](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=566)，“服务可访问性”。
3. [TS 22.101](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=605)，“服务方面；服务原则”。
4. [TS 22.185](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=2989)，“V2X 服务的服务要求”。
5. [TS 22.071](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=584)，“定位服务 (LCS)；服务描述”。
6. [TS 22.115](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=608)，“服务方面；计费和计费”。
7. [TS 22.153](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=617)，“多媒体优先服务”。
8. [TS 22.173](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=620)，“IP 多媒体核心网络子系统 (IMS) 多媒体电话服务和补充服务”。
9. [TS 22.186](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=3180)，“增强型 V2X 场景的服务要求”。



网站：https://www.3gpp.org/technologies/5g-system-overview