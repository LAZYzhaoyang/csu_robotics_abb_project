# 1、机械臂连接电脑方法

1.1 网线插入机械臂的X2网口

1.2 网线接入电脑，设置网络端口

IP地址可修改，但相应的连接的机械臂源码中使用socket库连接的IP地址也需要进行相应的修改，并且必须保证机械臂与连接的主机处于同一网段下才可保证正常连接

IP地址：192.168.125.4

子网掩码：255.255.255.0

端口：

Windows下：192.168.125.*（*为除1、4以外0~255内的任意数字）   Ubuntu下：0.0.0.0

# 2、修改机械臂源代码步骤：

2.1 按1中步骤连接电脑

2.2 在Windows下打开robot studio，在“控制器”界面点击“添加控制器”

2.3 显示连接上后，在“控制器”界面点击用户管理，选择切换用户

2.4 robotsudio 切换用户（不切换的话是使用系统默认的用户，不能修改）才能进行修改，以下是切换用户信息

用户名：college

密码：19911214

2.5 在 ”控制器“ 界面点击 ”请求写权限“ ，在示教器上显示是否同意后点击同意即可获得修改机械臂源代码权限

2.6 在robot studio的repid界面即可修改机械臂源代码

# 3、机械臂中几个关键的点（机械臂源代码中）

MyTool点：这是预先设置的机械臂工具坐标系原点

Target_10点：这是机械臂运动的参照点，可修改

pHome点：这是之前预设的一个拍照点，现在已经没什么用了

# 4、使用socket库连接电脑与机械臂

（主机作为服务器端，机械臂作为客户端接听服务器端口，必须保证服务端与客户端处于同一网段下，即IP地址前三位都一样）

4.1 server端（在主机端的python代码中调用socket库连接机械臂）

4.1.1 在python中建立socket类对象，用来创建TCPsocket连接，绑定服务器IP地址与端口

sock=socket.socket(family,type[,protocal]) 使用给定的地址族、套接字类型、协议编号（默认为0）来创建套接字。

family参数包括：

socket.AF_UNIX：只能够用于单一的Unix系统进程间通信

socket.AF_INET：服务器之间网络通信

socket.AF_INET6：IPv6

type参数包括：

socket.SOCK_STREAM：流式socket , for TCP

socket.SOCK_DGRAM：数据报式socket , for UDP

socket.SOCK_RAW：

原始套接字，普通的套接字无法处理ICMP、IGMP等网络报文，而SOCK_RAW可以；其次，SOCK_RAW也可以处理特殊的IPv4报文；此外，利用原始套接字，可以通过IP_HDRINCL套接字选项由用户构造IP头。

socket.SOCK_SEQPACKET：可靠的连续数据包服务

可选参数protocal：协议编号，默认为0

s.bind(address):

将套接字绑定到地址, 在AF_INET下,以元组（host,port）的形式表示地址.（服务器IP地址，端口）格式

4.1.2 监听建立的socket类对象

s.listen(backlog):

开始监听TCP传入连接。backlog指定在拒绝连接之前，操作系统可以挂起的最大连接数量。该值至少为1，大部分应用程序设为5就可以了。

4.1.3 等待客户端连接服务器端

4.1.4 接受来自客户端的连接请求 socket.accept（）

connection, client_address = sock.accept()：

接受TCP连接并返回（conn,address）,其中conn是新的socket套接字对象，可以用来接收和发送数据。address是连接客户端的地址。

4.1.5 以字符串形式向客户端发送命令和接收来自客户端的消息

data = connection.recv(bufsize[,flag])：

接受TCP套接字的数据。数据以字符串形式返回，bufsize指定要接收的最大数据量。flag提供有关消息的其他信息，通常可以忽略。data返回的是字符串形式的数据。

connection.sendall(string[,flag])：

完整发送TCP数据。将string中的数据发送到连接的套接字，但在返回之前会尝试发送所有数据。成功返回None，失败则抛出异常。同样客户端收到的是一个字符串。需要对接受到的字符串进行解码

4.2 client端（机械臂源码中）

4.2.1 创建socket套接字对象，连接服务器端地址

SocketClose socket1;

SocketCreate socket1;

SocketConnect socket1, "192.168.125.4", 4005;

4.2.2 向服务器端发送预先约定的字符串，确认连接成功 

SocketSend socket1\Str:="pass";（详情请看ABB官方提供的指令集说明书）

4.2.3 接收来自服务器端的消息

SocketReceive socket1\Str:=received_string\Time:=WAIT_MAX;（以字符串类型数据received_string接收来自服务器端的数据，最大等大时间：无限

其他socket库的使用请看链接：https://blog.csdn.net/rebelqsp/article/details/22109925

# 5、修改参照点Target_10的方法

5.1方法一：

5.1.1 打开示教器，点击程序数据

5.1.2 选择robotdata，选择Target_10，点击编辑菜单，选择修改程序数据，则会将当前机械臂吸头所在位置设置为Target_10的值

（机械臂程序中的ReTool程序其实就是相对于所给参照点做相对运动）

5.2 方法二：

5.2.1 连接电脑后，打开robotstudio，直接在源程序中找到Target_10常量所在位置，修改相应的值即可。
