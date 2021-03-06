---
layout:     post
title:      "聊聊Socket、TCP/IP、HTTP、FTP及网络编程"
subtitle:   "从知道不知道到知道知道。"
date:       2017-04-15 11:00:00
author:     "Jianxin Guo"
header-img: "img/post-bg-01.jpg"
catalog: true                       # 是否归档
tags:                               #标签
    - I/O
    - 网络通信
---


既然是网络传输，涉及几个系统之间的交互，那么首先要考虑的是如何准确的定位到网络上的一台或几台主机，另一个是如何进行可靠高效的数据传输。``这里就要使用到TCP/IP协议。``

<h2 class="section-heading">1.1 TCP/IP协议组</h2>
``TCP/IP协议（传输控制协议）由网络层的IP协议和传输层的TCP协议组成。``

```
IP层负责网络主机的定位，数据传输的路由，有IP地址可以唯一的确定Internet上的一台主机。

TCP层负者面向应用的可靠的或非可靠的传输机制，这就是网络编程的主要对象。
```
TCP/IP是个协议组，可分为三层次：应用层、传输层、应用层：

```
网络层：IP协议、ICMP协议、ARP协议、RARP协议和BOOTP协议；

传输层：TCP协议与UDP协议；

应用层：FTP、HTTPA、TELNET、SMTP、DNS等协议；
```

``HTTP是应用层协议，其传输都是被包装成TCP协议传输。可以用socket实现HTTP。socket是实现传输层协议的一种编程API，可以是TCP，也可以是UDP``


<h2 class="section-heading">1.2 TCP</h2>
``TCP --- 传输控制协议，提供的是面向连接、可靠的字节流服务。``当客户端和服务器端彼此交换数据前，必须现在双方之间建立一个TCP连接，之后才能传输数据。TCP提供超时重发，丢弃重复数据，检验数据，流量控制等功能，保证数据能从一端传到另一端。理想状态下，TCP链接一旦建立，在通信双方中的任何一方主动关闭连接前，TCP连接都将被一直保持下去。断开接连时服务器和客户端均可以主动发起断开TCP连接的请求。
``TCP是一种面向连接的保证可靠传输的协议。``通过TCP协议传输，得到的是一个顺序的无差错的数据流。发送方和接收方的成对的两个socket之间必须建立连接，以便在TCP协议的基础上进行通信，当一个socket（通常是server socket）等待建立连接时，另一个socket可以要求进行连接，一旦这个两个socket连接起来，它们就可以进行双向数据传输，双方都可以进行发送或接受操作。

TCP特点：

1.tcp是面向连接的协议，通过三次握手建立连接，通讯完成时要拆除连接，由于TCP是面向连接协议，所以只能用于点对点的通讯。而且建立接连也需要消耗时间和开销。

2.tcp传输数据无大小限制，进行大数据传输。

3.tcp是一个可靠的协议，它能保证接收方能够完整正确地接收到发送方发送的全部数据。


要了解TCP，一定要知道所谓的三次握手，就是发送数据前必须建立的连接叫三次握手，握手完了才开始发的，这也就是面向连接的意思。

```
第一次握手：客户端发送syn包（sny=j）到服务器，并进入SYN_SEND状态，等待服务器确认；

第二次握手：服务器受到syn包，必须确认客户端的syn（ack=j+1），同时自己也发送syn包（syn=k），即syn+ack包，此时服务器进去syn——recv状态；

第三次握手：客户端收到服务器的syn+ack包，向服务器发送确认包ack（ack=k+1），次包发送完毕，客户端和服务器进入established状态，完成三次握手；
```

使用情况

``TCP发送的包有序号，对方收到包后要给一个反馈，如果超过一定时间还没收到反馈就自动执行超时重发，因此TCP最大的优点就是可靠。``一般网页（http）、邮件（smtp）、远程连接（telnet）、文件（ftp）传送就用tcp。

``TCP在网络通信上游极强的生命力``，例如远程连接（Telnet）和文件传输（FTP）都需要不定长读的数据被可靠地传输。``但是可靠的传输是要付出代价的，对书序内容正确性的检验必然占用计算机的处理时间和网络的带宽``，因此TCP传输的效率不如UDP高。


<h2 class="section-heading">1.3 UDP</h2>
``UDP：用户数据报协议，是一个无连接的简单的面型数据报的传输层协议。``UDP不提供可靠性，它只是把应用程序传给IP层的数据报发送出去，但是并不能保证它们能到达目的地。由于UDP在传输数据报前不用在客户和服务器之间建立一个连接，且没有超时重发等机制，故而传输速度很快。

``UDP是一种面向无连接的协议，每个数据报都是一个独立的信息，``包括完整的源地址或目的地址，它在网络上以任何可能的路径传往目的地，``因此能否到达目的地，到达目的地的时间以及内容的正确性都是不能被保证的。``

UDP特点：

1.UDP是面向无连接的通讯协议，UDP数据包括目的端口号和源端口号信息，由于通讯不需要连接，所以可以实现广播发送。

2.UDP传输数据时有大小限制，每个被传输的数据报必须限定在64KB之内。

3.UDP是一个不可靠的协议，发送方所发送的数据报并不一定以相同的次序到达接受方。


适用情况

UDP是面向消息的协议，通信时不需要建立连接，数据的传输自然是不可靠的，UDP一般用于多点通信和实时的数据业务，比如语音广播、视频、QQ、DNS（域名解释）。注重速度流程。

UDP操作简单，而且仅需较少的监护，因此通常用于局域网高可靠性的分散系统中c/s应用程序。例如视频会议系统，并不需要音频数据绝对的正确，只要保证连贯性就可以了，这种情况下显然使用UDP会更合理一些。

<h2 class="section-heading">1.4 socket</h2>
``socket通常也称作套接字，用于描述IP地址和端口，是一个通信链的句柄。``网络上的二个程序通过一个双向的通讯连接实现数据的交换，这个双链路的一端为一个socket，``一个socket由一个IP地址和一个端口号唯一确定。``应用程序通常通过socket向网络发出请求或者应答网络请求。Socket是TCP/IP协议的一个十分流行的编程界面，但是，socket所支持的协议种类也不光TCP/IP一种，因此两者之间没有必然联系。在java环境下，socket编程主要是指基于TCP/IP协议的网络编程。

socket通讯过程：服务端监听某个端口是否连接请求，客户端向服务器端发送连接请求，服务端收到连接请求向客户端发出接收消息，这样一个连接就建立起来了。客户端和服务端都可以相互发送消息与对方进行通讯。

``socket是应用层与TCP/IP协议族通信的中间软件抽象层，它是一组接口。``在设计模式中，``socket其实就是一个门面模式，它把复查的TCP/IP协议族隐藏在socket接口后面，``对于用户来说，``一组简单的接口就是全部，让socket去组织数据，已复核指定的协议。``

``由于通常情况下socket链接就是TCP连接，``因此socket连接一旦建立，通信双方即可开始相互发送数据内容，知道双方连接断开，但实际网络应用中，客户端到服务器之间的通信往往需要穿越多个中间节点，例如路由器、网关、防火墙等，``大部分防火墙默认会关闭长时间处于非活跃状态的连接导致socket连接断开，因此需要通过轮询告诉网络，该连接处于活跃状态。``

1.socket概念：
``socket是通信的基石，是支持TCP/IP协议的网络通信的基本操作单元。``它是网络通信过程中端点的抽象表示，包含进行网络通信必须的五种信息：连接使用的协议，本地主机的IP地址，本地进程的协议端口，远程主机的IP地址，远程进程的协议端口。

应用层通过传输层进行数据通信时，TCP会遇到同事为多个应用程序提供并发服务的问题。``多个TCP连接或多个应用程序可能需要通过同一个TCP协议端口传输数据。为了区别不同的应用程序和连接，许多计算机操作系统为应用程序与TCP/IP协议交互提供了socket接口。应用层可以和传输层通过socket接口，区分来自不同应用程序进程或网络连接的通信，实现数据传输的并发服务。``

2.简历socket连接

建立socket连接至少需要一对套接字，``其中一个运行与客户端，称为ClientSocket，另一端运行与服务器端，称为ServletSocket。``

socket之间的连接过程分为三个步骤：``服务器监听、客户端请求，连接确认。``

```
服务器监听：服务器端socket并不定位具体的客户端socket，而是出于等待连接的状态，实时监控网络状态，等待客户端的连接请求；

客户端请求：指客户端的socket提出连接请求，要连接的目标是服务器端的socket。为此，客户端的socket必须首先描述它要连接的服务器的socket，指出服务器端套接字的地址和端口号，然后就想服务器端socket提出连接请求。

连接确认：当服务器端socket监听到或者说接收到客户端socket连接请求时，就响应客户端socket的请求，简历一个新的线程，把服务端socket的描述发给客户端，一旦客户端确认了此描述，双方就正式建立连接。而服务器端socket继续处于监听状态，继续接受其他客户端socket的连接请求。
```

3.socket连接与TCP连接

创建socket连接时，可以指定使用的传输层协议，socket可以支持不同的传输层协议（TCP或UDP），当使用TCP协议进行连接时，该socket连接就是一个TCP连接。

适用情况
很多情况下，需要服务器端主动向客户端推送数据，保持客户端与服务器数据的实时与同步。此时若双方简历的是socket连接，服务器就可以直接将数据传送给客户端。

<h2 class="section-heading">1.5 http</h2>
``http协议是建立在TCP协议之上的一种应用，http连接使用的是请求-响应的方式，不仅在请求时需要先建立TCP连接，而且需要客户端向服务器发出请求后，请求中包含请求方法、url。协议版本以及相关的MIME样式的消息，服务器端才能回复数据，包含消息的协议版本、一个成功和失败码以及相关的MIME式样的消息。在请求结束后，会主动释放连接。从建立连接到关闭连接的过程称为一次连接。由于HTTP在每次请求结束后都会主动释放连接，因此HTTP连接是一种短连接，要保持客户端程序的在线状态，需要不断地想服务器发起连接请求。通常的做法是即时不需要获得任何数据，客户端也保持每隔一段固定时间向服务器发送一次保持连接的请求，服务器在受到请求后对客户端进行回复，表面知道客户端在线。弱服务器长时间无法受到客户端请求，则认为客户端下线，若客户端长时间无法收到服务器的回复，则认为网络已经断开。

``HTTP/1.0为每一次HTTP的请求/响应建立一条新的TCP链接，因此一个包含HTML内容和图片的页面将需要建立了多次的短期的TCP链接。``一次TCP链接的建立将需要3次握手。

另外，为了获得适当的传输速度，则需要TCP花费额外的回路链接时间（RTT)。每一次链接的建立需要这种经常性的开销，而其并不带有实际有用的数据，只是保证链接的可靠性，因此HTTP/1.1将只建立一次的链接而重复地使用它传输一系列的请求/响应消息，因此减少了链接建立的次数和经常性的连接开销。

结论：HTTP是应用层协议，其传输都是被包装成TCP协议传输。一颗永socket实现http。socket是实现传输层协议的一种编程API，可以是TCP，也可以是UDP。

【适用情况】

若双方建立的是HTTP连接，则服务器需要等到客户端发送一次请求后才能将数据传回给客户端，因此，客户端定时向服务器端发送连接请求，不仅可以保持在线，同时也是在询问服务器是否有新的数据，如果有就将数据传给客户端。

<h2 class="section-heading">1.6 FTP</h2>

``文件传输协议是TCP/IP网络上两台计算机传送文件的协议，FTP是在TCP/IP网络和internet上最早使用的协议之一，它属于网络协议组的应用层。FTP客户机可以给服务器发出命令来下载文件，上传文件，创建或改变服务器上的目录。``




