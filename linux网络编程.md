## Socket编程  

## **1** :**网络套接字** 

$$
网络套接字（Socket）是在计算机网络中进行网络通信的一种编程接口或抽象（是应用层与TCP/IP协议族通信的中间软件抽象层，它是一组接口。）。它提供了一种机制，使得不同计算机之间可以通过网络进行数据传输和通信。​套接字可以被看作是在网络上的两个节点之间建立的一个通信端点。它允许应用程序通过发送和接收数据来进行网络通信。套接字提供了一组API（Application Programming Interface）或函数，使得开发人员可以使用常见的网络协议（如TCP、UDP等）进行数据传输。​使用套接字，应用程序可以实现以下功能：​建立网络连接：套接字允许应用程序在不同计算机之间建立网络连接。通过指定目标计算机的地址和端口号，应用程序可以创建一个套接字，并与远程计算机建立连接。​发送和接收数据：套接字提供了发送和接收数据的方法。应用程序可以使用套接字发送数据到远程计算机，或者从套接字接收远程计算机发送的数据。​处理不同网络协议：套接字支持多种常见的网络协议，如TCP（Transmission Control Protocol）和UDP（User Datagram Protocol）。这使得应用程序可以选择适合其需求的协议，并使用套接字进行相应的数据传输。​处理网络错误和异常：套接字提供了处理网络错误和异常的机制。应用程序可以检测和处理网络连接中可能发生的错误或异常情况，以确保可靠的数据传输和通信。​套接字在网络编程中被广泛使用，用于构建各种类型的网络应用程序，包括Web服务器、网络游戏、聊天应用程序等。通过套接字编程，开发人员可以控制和管理网络通信，实现数据的可靠传输和实时交互。
$$
一个文件描述符指向一个文件套接字

在通信过程中，套接字是一定成对出现的

## **2: 网络字节序**

$$
在计算机网络中，网络字节顺序（Network Byte Order）具有重要的作用，主要用于解决不同计算机体系结构和操作系统之间的数据传输问题。以下是网络字节顺序的几个关键作用：

1数据交换：计算机网络中的通信涉及多个计算机或设备之间的数据传输。不同计算机或设备可能使用不同的字节顺序来表示多字节数据，例如大端字节顺序和小端字节顺序。使用网络字节顺序，可以确保数据在不同系统之间正确地解释和传递。

2网络协议：网络协议是定义数据在网络中传输和交换的规范。许多网络协议要求数据在传输之前或之后进行字节顺序的转换，以确保数据的一致性和正确性。规定使用网络字节顺序可以简化协议的实现和跨平台的兼容性。

3网络编程：在进行网络编程时，开发人员需要处理不同系统之间的数据传输和解析。使用网络字节顺序可以确保数据在不同系统之间的正确传输和解释，避免因字节顺序不同而导致的数据错误。

4数据序列化和反序列化：在将数据从内存中序列化为字节流，或者从字节流中反序列化回内存时，需要考虑字节顺序。使用网络字节顺序可以保证数据在序列化和反序列化过程中的一致性，使得不同系统之间可以正确地进行数据交换。

总之，网络字节顺序在计算机网络中起到标准化数据表示和传输的作用，确保不同系统之间的数据交换的正确性和一致性。它是网络通信和跨平台兼容性的重要基础。
$$


小端法（PC本地存储）：高位存高位，底位存底位

大端法（网络存储）：高位存底位，底位存高位

​	1:转字节序函数

``` linux
		1：htonl : 本地字节序转网络字节序  (IP)地址 					

​		2:  htons:  本地字节序转网络字节序 (PORT)端口

​		3：ntohl：网络字节序转本地字节序

​		4:  ntohs：网络字节序转本地字节序
```



​	2:IP地址转化函数

``` linux
	这里的IP实质是string类型的，所以我们应该将string类型转换为int类型

 int inet_pton(int af,const char*src,void *dst); 将IP(本地字节序  ***主机字节序)转换为网络字节序储存的形式***

>   *  **第一个参数**:  (int af)  表示IP类型  **AF_INET** （ipv4） **AF_INET6**(ipv6)
>   *  **src** : IP地址（点分十进制）
>   *  **dst** ：转换后的网络字节序的IP地址

​													
```

``` linux
const char  *inet_ntop(int af,const void *src,char *dst,socklen_t size); ***将网络字节序转化为主机字节序，本地字节序的IP（string）***

> *  **第一个参数**:  (int af)  表示IP类型  **AF_INET** （ipv4） **AF_INET6**(ipv6)
> *  **src** ：网络字节序IP地址
> *  **dst**:   本地字节序
> *  **size** :   dst**的大小**
```



## **3：sockaddr地址结构**

$$
struct sockaddr 该地址数据类型是最早的一种类型，现在大多数是(1) struct sockaddr_in(主要是IPV4)  (2) ，struct sockaddr_un  (3) ，struct sockaddr_in6 （主要是IPV6）
$$



**用法：** 

![image-20231107223152398](images/linux网络编程/image-20231107223152398.png)

``` linux
struct sockaddr_in addr;
首先我们需要初始化一下addr中的成员
解析addr中该结构体中的成员 (sin_famlify  sin_port  struct sin_addr)
addr.sin_family = AF_INET / AF_INET6
addr.sin_port = htons(9527)

（1）int dst;
	inet_pton(AF_INET,"192.157.22.45",(void*)dst);
	addr.sin_addr.s_addr = dst
（2）addr.sin_addr.s_addr = htonl(INADDR_ANY) 取出系统中有效的任意IP地址，二进制类型

```



## 4 网络套接字函数

**服务器中的函数**

1. socket函数；int socket(int domian,  int type,  int protocol);  创建一个套接字  

``` linux
   damain ：是IP地址协议类型  AF_INET / AF_INET6   AF_UNIT

   type : 表示创建的套接字所选用的数据传输协议    SOCK_STREAM（TCP） /  SOCK_DGRAM(UDP)

   protocol：协议,常见的协议有IPPROTO_TCP、IPPTOTO_UDP、 IPPROTO_SCTP、IPPROTO_TIPC他们分别对应这TCP传输协议,UDP传输协议,STCP传输协议,TIPC传输协议.当protocol为0时,会自动选择type类型对应的默认协议.
   
成功返回的是一个文件描述符，失败返回-1 

```

2. bind函数  int bind(int sockfd , const struct sockaddr  * addr  , socklen_t  addrlen); 给socket绑定地址结构 （IP+port）

   **用这个函数的时候记得要加 #include <arpa/inet.h>**

``` linux
sockfd： 是socket函数的返回值
addr ：struct sockaddr_in addr;
	  首先我们需要初始化一下addr中的成员
      解析addr中该结构体中的成员 (sin_famlify  sin_port  struct sin_addr)
      addr.sin_family = AF_INET / AF_INET6
      addr.sin_port = htons(9527)

     （1）int dst;
	     inet_pton(AF_INET,"192.157.22.45",(void*)dst);
	     addr.sin_addr.s_addr = dst
     （2）addr.sin_addr.s_addr = htonl(INADDR_ANY) 取出系统中有效的任意IP地址，二进制类型
     
addrlen：sizeof(addr) 地址结构大小

成功返回0，失败返回-1
```

3. listen函数  int listen (int sockfd , int backlog) 设置同时与服务器建立的上限数 （同时进行3次握手的客户端数量）

``` linux
sockfd：是socket函数的返回值
backlog：上限数值 最大128
```

4. accept函数 int accept(int sockfd , struct sockaddr *addr , socklen_t * addr_len);  阻塞等待客户端建立连接，成功的话，返回一个与客户端成功连接的socket文件描述符

``` linux
sockfd：socket函数的返回值
addr：(传入参数)成功与服务器 建立连接的那个客户端的地址
addr_len：（传入传出） 
		socklen_t clit_addr_len = sizeof(addr);
		函数传入 &clit_addr_len
返回值：成功：能与服务器进行数据通信的socket对应的文件描述符
       失败：-1
```

#### <u>创建一个服务端的程序，并用nc命令连接它</u>

``` linux
#include <stdio.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <arpa/inet.h>
#include <unistd.h>


#define SER_PORT 9867
int main(int argc,char *argv[]){
    int fd = 0;
    int cfd = 0;
    int dfg ;//该变量表示服务端读取客户端发送消息的长度
    
    char buf[BUFSIZ]，client_ip[1024];
    struct sockaddr_in seraddr,cliaddr;//初始化服务端的地址结构，
    
    socklen_t clit_len; 客户端地址结构
    
    seraddr.sin_family = AF_INET;
    seraddr.sin_port  = htons(SER_PORT);
    seraddr.sin_addr.s_addr = htonl(INADDR_ANY);
    
    fd = socket(AF_INET,SOCK_STREAM,0);
    if(fd ==-1){
        perror("socket create error");
    }
    
    bind(fd,(struct sockaddr*) &seraddr,sizeof(seraddr));
    listen(fd,128);
    clit_len = sizeof(cliaddr);
    cfd = accept(fd,(struct sockaddr*) &cliaddr,&clit_len);
    if(cfd == -1){
        perror("accept error");
    }
    inet_ntop(AF_INET,&cliaddr.sin_addr.s_addr,client_ip,sizeof(client_ip));
    std::cout << "client ip" << client_ip
    		<< "client port" << ntohs(cliaddr.sin_port);
    
    while(true){
    dfg = read(cfd,buf,sizeof(buf));
    write(STDOUT_FILENO,buf,dfg);
    for(int i =0;i<dfg;i++){
        buf[i] = toupper(buf[i]);
    }
    write(cfd,buf,dfg);
    }
    close(fd);
    close(cfd);

}
```

**获取客户短地址结构** (在上数代码中填入)

``` linux
我们在服务端用accept函数用接受客户端的连接请求，	创建一个新的套接字与客户端连接，
函数参数为
sockfd 是服务器端监听套接字的文件描述符。
addr 是一个指向 struct sockaddr 结构体的指针，用于存储客户端的地址信息。
addrlen 是一个指向 socklen_t 类型的指针，用于指定 addr 结构体的长度。
所以我们可以在建立连接的同时，获取到与服务端连接的客户端地址结构
struct sockaddr_in clientaddr;
char client_ip[1024];
inet_ntops(AF_INET,&clientaddr.sin_addr.a_addr,client_ip,sizeof(client_ip))
std::cout << "client ip:" << client_ip
		<< "client port:" << ntohs(clientaddr.sin_port) << std::endl;

```



### **客户端的函数**

1. socket函数 与客户端的socket函数定义一样

**客户端**不用绑定地址结构，系统会“隐式绑定”

1. connect函数 int connect(int sockfd , const struct sockaddr  * addr  , socklen_t  addrlen)  使用现有的socket 与服务器建立连接

``` linux
sockfd：是socket函数的返回值
addr：是服务器的地址结构
struct sockaddr_in server_addr;
server_addr.sin_family = AF_INET;
server_addr.sin_port 这里和服务端中的port完全一样
inet_pton(AF_INET,"服务端的ip"，&server_addr.sin_addr.s_addr)
addrlen：服务器地址结构大小
```

``` linux

#include <iostream>
#include <sys/types.h>
#include <sys/socket.h>
#include <arpa/inet.h>
#include <cstring>
#include <unistd.h>

#define SER_PORT 9867
int main(){
    int cli ;
    char buf[BUFSIZ];
    int conter = 10;
    struct sockaddr_in servr_addr;
    servr_addr.sin_family = AF_INET;
    servr_addr.sin_port = htons(SER_PORT);
    inet_pton(AF_INET,"127.0.0.1",&servr_addr.sin_addr.s_addr);
    cli = socket(AF_INET,SOCK_STREAM,0);
    if(cli==-1){
        perror("sock create error");
    }
    int ret = connect(cli,(struct sockaddr*)&servr_addr,sizeof(servr_addr));
    if(ret!=0){
        perror("connect is error");
    }
    while(conter--){
        write(cli,"hello\n",6);
        ret = read(cli,buf,sizeof(buf));
        write(STDOUT_FILENO,buf,ret);
        sleep(1);
    }
    close(cli);

}
```

