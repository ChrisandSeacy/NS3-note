# 0. NS-3 概念概述

1. 关键抽象： 节点，应用，频道，网络设备，拓扑助手

   ```C++
   //节点Node
   在ns3中，基本的计算设备抽象成为节点，这种抽象在C++中由class Node表示。可以将Node视为您将添加功能的计算机，一种是添加应用程序、协议栈和外围卡等相关驱动程序，以使能完成计算机的工作。
   ```

   ```C++
   //应用Application
   ns3 中的application指的是用户层级的应用软件。在C++中由class表示Application，该类Application提供了在模拟中管理我们的用户级应用程序版本的表示的方法。Application开发人员应在面向对象编程意义上专门化 该类以创建新应用程序。在本教程中，我们将使用 Application名为UdpEchoClientApplicationand 的类的特化UdpEchoServerApplication。如您所料，这些应用程序组成了一个客户端/服务器应用程序集，用于生成和回显模拟网络数据包。
   ```

   ```C++
   //频道channel
   抽象类Channels，表示网络中数据流经的通道。
   该类Channel提供了管理通信子网对象并将节点连接到它们的方法。 Channels也可以由开发人员在面向对象编程意义上专门化。Channel专业化可以建模像电线这样简单的东西。 专业人士 Channel还可以对大型以太网交换机或无线网络中充满障碍物的三维空间等复杂的事物进行建模。
   ```

   ```C++
   //网络设备（网络驱动）
   如果没有软件驱动程序来控制硬件，NIC 将无法工作。在 Unix（或 Linux）中，一块外围硬件被归类为 设备。使用设备驱动程序控制设备，使用 统称为网络设备的网络设备驱动程序控制网络设备 (NIC) 。在 Unix 和 Linux 中，您使用eth0等名称来指代这些网络设备。
   ```

   ```C++
   //拓扑助手（分配IP地址，MAC地址，让Node，NetDevices，Channels合作起来）
   在真实的网络中，您会发现带有添加（或内置）NIC 的主机。在ns-3中，我们会说您会找到Nodes附加NetDevices的 . Nodes在大型模拟网络中，您需要在NetDevices和 之间安排许多连接Channels。
   
   由于连接NetDevices到Nodes、NetDevices 到Channels、分配 IP 地址等是ns-3中的常见任务，因此我们提供了所谓的拓扑帮助器以使这些操作尽可能简单。例如，可能需要许多不同的 ns-3核心操作来创建一个 NetDevice、添加一个 MAC 地址、将该网络设备安装Node在NetDevice一个Channel. 甚至需要更多的操作将多个设备连接到多点通道，然后将各个网络连接到互联网中。为了您的方便，我们提供了拓扑帮助器对象，这些对象将这些不同的操作组合成一个易于使用的模型
   ```

   

2. 第一个ns-3 脚本： 样本文件，模块包括，NS3明明空间，日志记录，主要功能，拓扑助手（节点容器，点对点助手，网络设计容器，InternetStackHelper），IPv4AddressGHelp），应用（UdpEcvhoServerGHelper， UdpEchoClientHelper），模拟器，模拟器什么时候停止？

```C++
//1.模块包含(就是大量的头文件)
NS3包含了更多的模块项，这些模块都被制成大颗粒度（递归被添加，让编写脚本更容易）。
  
这些文件放在一个名为ns3的目录中，帮助包含文件冲突。打开ns3/core-module.h将在src/core下载的发行版目录中找到ns3板块。当构建的时候，ns3将根据您的配置将有文件放置在ns3在相应目录下的build/debug目录中。build/optimizedCMake还会自动生成一个模块包含文件来加载所有公共头文件。
  
../../build/include/ns3会发现上面显示的四个模块包含文件。
```

```C++
//NS3命名空间

using namespace ns3;
```

```C++
//日志记录
NS_LOG_COMPONENT_DEFINE ("FirstScriptExample");

这一行声明了一个名为日志记录组件FirstScriptExample，允许您通过引用名称来启动和禁用控制台消息日志记录。
  
Notes：建议去看Logging模块的Doxygen文档，去看NS_LOG_COMPONENT_DEFINE操作。
  
如何找文档？
【顺序】： 项目网站去找ns-3 project，然后在导航栏找“文档”，然后有一个指向“最新版本”的链接，这是ns3的最新稳定版本的文档。如果选择API文档，那就将被带到ns-3 API文档页面。
  
  ns3文档是一本很好的导航书，有很多模块（包含文件）的概念。ns3的ModulesModulesCoreDebugging toolsLoggingLogging 是日志。
```

```C++
//主要功能

//主函数
int main(int argc,char* argv[]){
  Time::SetResolution (Time::NS); //时间戳，默认为1纳秒，模拟开始会释放内存
  LogComponentEnable("UdpEchoClientApplication", LOG_LEVEL_INFO);
LogComponentEnable("UdpEchoServerApplication", LOG_LEVEL_INFO);
  
  //在Logging组件文档，可以看到这句话是在UdpEcho的客户端和服务器上启用了INFO级别的调试日志记录。这导致应用程序在模拟期间发送和接受数据包时打印出来的信息。
}
```



下面开始创建拓扑和运行模拟的业务：

```c++
//节点容器（批量的创造节点、管理节点）
NodeContainer nodes;
nodes.Create(2);

//通过NodeContainer该类的文档，之前讲过Node是计算机，可以在其中加入协议栈、应用程序和外围卡等。NodeContainer拓扑助手帮助我们批量去创建和管理Node对象。正如Doxygen中所描述，容器调用ns-3的系统，创造两个Node对象，并在内部存储指向这些对象的指针。
```

当我们构建完节点，考虑的是如何将节点链接到一个网络中，下面我们构建一个点对点的链接。

```C++
//点对点助手（拓扑辅助对象）
对于一个计算机，我们两个关键的抽象是NetDevice和Channel，也就是外设网卡和网线。那么在现实中，什么样的网卡就会对应什么样的网线，这个是匹配不能分离的。所以TopologyHelpers就是做这种事情的，将单个PointToPoint Helper来配置PointToPointNetDevice和PointToPointChannel：
 
PointToPointHelper pointToPoint;
//在栈上实例化一个对象。

pointToPoint.SetDeviceAttribute ("DataRate", StringValue ("5Mbps"));
//告诉创建对象，对PointToPointNetDevice的DataRate的这个属性，设置成“5Mbps”。

pointToPoint.SetChannelAttribute ("Delay", StringValue ("2ms"));
//对PointToPointHelper使用“2ms”作为PointToPointChannel的属性。

也就是说PointToPointHelper这个类包含两个类的类对象，可以通过调用接口直接对这两个对象进行属性的赋值。
```



创造网络设备：现在我们有两个节点node，然后有网卡和网线，那么接下来就可以组装成网络设备了。用NetDevice Container来保存他们。

```C++
NetDeviceContainer devices;
devices = pointToPoint.Install (nodes);

//重点讲一下第二行，首先参数是nodes，所以Install的参数必须是NodeContainer，在内部，对于每一个nodes中的Node（p2p必须是2个Node），pointTopointNetDevice创建了网卡并放到NetDeviceContainer中，并创建了PointToPointChannel，返回两个节点，每个节点都有网卡，并且他们之间会有一个点对点的通道。

//Notes？ 如果是多个节点怎么办呢？？
```



现在是添加网络协议栈-> IP地址-> 应用

```C++
//关于网络协议栈InternetStackHelper
InternetStackHelper stack;
stack.Install (nodes);

注意此时参数是NodeContainer，是对Node加堆栈（TCP，UDP，IP等等）。
  
//Notes： 什么是网络协议栈&&&&&&&&&&&&&&&&&&&&&&&&
```

```C++
//Ipv4AddressHelper管理IP地址的分配

Ipv4AddressHelper address;
address.SetBase ("10.1.1.0", "255.255.255.0");
//这个助手会从10.1.1.0按照子网255.255.255.0分配，如果不小心分配成相同的地址，这是一个很难排除的错误，就会产生致命错误。

Ipv4InterfaceContainer interfaces = address.Assign (devices);
//用Ipv4Interface对象在IP地址和设备之间建立关联
```



下面我们需要两个应用程序UdpEchoServerHelper 和 UdpEcho ClientHelper

```C++
//应用程序 - UdpEchoServerHelper
UdpEchoServerHelper echoServer (9); 

//这个时候就是创造对象并传递参数的过程，传递的是端口号
ApplicationContainer serverApps = echoServer.Install (nodes.Get (1));
//这里有一个 C++隐式转换在起作用，它获取（返回一个指向节点对象的智能指针）的结果，并在构造函数中将其用于一个未命名的对象，然后将其传递给nodes.Get (1)Ptr<Node>NodeContainerInstall. 
//注意这个时候是给容器中第一个Node添加上了Application

serverApps.Start (Seconds (1.0));
serverApps.Stop (Seconds (10.0));
//什么时候开始这个流量呢，基于NS3的时间戳，会把Seconds转换成NS3::Time
//将导致回显服务器应用程序Start在进入模拟的一秒时（启用自身）并在进入模拟Stop的十秒时（禁用自身）。由于我们已经声明了一个模拟事件（应用程序停止事件）要在 10 秒后执行，因此模拟将持续至少10 秒。
```

```C++
//UdpEchoClientHelper 客户端的应用程序

UdpEchoClientHelper echoClient (interfaces.GetAddress (1), 9);
//所以服务器在找的时候，必须要制定连接的服务器和服务器的端口。有点像connect

echoClient.SetAttribute ("MaxPackets", UintegerValue (1));
echoClient.SetAttribute ("Interval", TimeValue (Seconds (1.0)));
echoClient.SetAttribute ("PacketSize", UintegerValue (1024));

ApplicationContainer clientApps = echoClient.Install (nodes.Get (0));


clientApps.Start (Seconds (2.0));
clientApps.Stop (Seconds (10.0));
```



模拟器运行 Simulator Run

```C++
Simulator::Run ();

//我们实际上将模拟器中的事件安排在 1.0 秒、2.0 秒和两个事件在 10.0 秒。当Simulator::Run被调用时，系统将开始查看预定事件列表并执行它们。首先它将在 1.0 秒时运行事件，这将启用回显服务器应用程序（此事件反过来可能会安排许多其他事件）。然后它将运行计划为 t=2.0 秒的事件，这将启动 echo 客户端应用程序。同样，此事件可能会安排更多事件。echo 客户端应用程序中的启动事件实现将通过向服务器发送数据包来开始模拟的数据传输阶段。


//模拟器是什么时候停止的：
 当这个出发的事件链不断减少，模拟进入空闲状态，一旦没有进一步的时间需要Simulator::返回，那么模拟就完成了，接下来就是清理工作，调用Simulator::Destroy来完成，以便在模拟器中插入挂钩以销毁所有已创建的对象。
   一般来说，如果没有事件发生的话，也就是任务队列中不再有其他的任务之后，Simulator::Run()就会返回，这样就会造成模拟器的停止。
   但是对于一个自我维持的时间，自我维持时间总是重新安排自己的时间，那么必须通过Stop函数来解决。
   有些时候，对于一些包含重复时间的协议和模块，例如Flow Monitor（定期检查丢失的数据包），RIPng（定期广播路由表更新）等等，那么这些就是需要仿真时钟和机器时钟对齐，并且必须调用Stop。
   
Simulator::Stop (Seconds (11.0));
   Simulator::Run ();
   Simulator::Destroy ();
   return 0;
 }
```



3. 构建你的脚本

有了源代码之后，如何运行脚本呢？

```C++
//把源代码放到临时目录scratch中
$ cp examples/tutorial/first.cc scratch/myfirst.cc
  
//使用ns3去构建第一个示例脚本
 $ ./ns3 build
  
//运行该例子
$ ./ns3 run scratch/myfirst
  
//运行的结果
At time +2s client sent 1024 bytes to 10.1.1.2 port 9
At time +2.00369s server received 1024 bytes from 10.1.1.1 port 49153
At time +2.00369s server sent 1024 bytes to 10.1.1.1 port 49153
At time +2.00737s client received 1024 bytes from 10.1.1.2 port 9
```



4. 文件的构造

```C++
//示例脚本在examples目录中
1. tutorial 子目录是教学源代码，我们的first.cc就在里面
2. 源代码主要在src中，模拟器核心在src/core/model子目录中
3. 助手代码可以在src/applications/helper找到
```

