#### windows共享文件夹
同一局域网内能使用

\\PC-20230204GLKY\Users\Administrator\Desktop\第二套答辩PPT
![](img/Pasted%20image%2020230605121020.png)

#### ipconfig查询ip
![](img/Pasted%20image%2020230605121041.png)


#### socket编程两台主机字符通信
要在同一内部网络下的两台主机之间发送字符流，您可以使用Socket编程来实现。以下是基本步骤：

1. 创建一个服务器端：在其中一台主机上创建一个服务器端，以侦听来自另一台主机的请求。在该主机上运行一个程序，该程序通过创建服务器Socket对象来等待请求。
要通过socket编程实现两台电脑之间传递"Hello, World!"消息，需要编写两个程序，一个作为客户端，一个作为服务器。

以下是一个简单的示例程序，可以让一台电脑作为服务器，另一台电脑作为客户端。该程序将在两台电脑之间传递"Hello, World!"消息。

客户端程序：

```python
import socket

# 创建TCP socket对象
client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# 服务器IP地址和端口号
server_address = ('192.168.1.10', 9999)

# 连接服务器
client_socket.connect(server_address)

# 发送消息到服务器
message = 'Hello, World!'
client_socket.send(message.encode())

# 接收服务器的响应
response = client_socket.recv(1024)
print(response.decode())

# 关闭连接
client_socket.close()
```

在这个程序中，首先创建了一个TCP socket对象。接着指定了服务器的IP地址和端口号，然后使用`connect()`方法连接服务器。接下来，发送消息到服务器，并等待服务器的响应。最后，关闭连接。

服务器程序：

```python
import socket

# 创建TCP socket对象
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# 服务器IP地址和端口号
server_address = ('192.168.1.10', 9999)

# 绑定socket对象到服务器地址和端口号
server_socket.bind(server_address)

# 开始监听客户端连接
server_socket.listen(1)
print('Server listening on', server_address)

while True:
    # 等待客户端连接
    client_socket, client_address = server_socket.accept()
    print('Received connection from', client_address)

    # 接收客户端消息
    message = client_socket.recv(1024)
    print(message.decode())

    # 发送响应消息到客户端
    response = 'Hello, client!'
    client_socket.send(response.encode())

    # 关闭连接
    client_socket.close()
```

在这个程序中，首先创建了一个TCP socket对象。然后指定了服务器的IP地址和端口号，并使用`bind()`方法将socket对象绑定到服务器地址和端口号。接着使用`listen()`方法开始监听客户端连接。在无限循环中，等待客户端连接请求，并接受连接。一旦接收到连接请求，服务器将接收消息，并发送响应消息到客户端，并关闭连接。

在运行这个程序之前，需要确保服务器和客户端之间可以互相访问。此外，需要确保在运行程序时，服务器程序先运行，客户端程序后运行。

以上是一个简单的实现"Hello, World!"消息传递的示例程序，完整的socket编程还需要考虑连接错误处理、超时等问题。

#### 命令行简化
在命令行工具中，可以使用`argparse`模块来实现命令行参数解析。以下是完整的代码示例：

```python
import argparse

import socket

def client(ip, port, message):
    # 创建TCP socket对象
    client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

    # 连接服务器
    server_address = (ip, port)
    client_socket.connect(server_address)

    # 发送消息到服务器
    client_socket.send(message.encode())

    # 接收服务器的响应
    response = client_socket.recv(1024)
    print(response.decode())

    # 关闭连接
    client_socket.close()

def server(ip, port):
    # 创建TCP socket对象
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

    # 绑定socket对象到服务器地址和端口号
    server_address = (ip, port)
    server_socket.bind(server_address)

    # 开始监听客户端连接
    server_socket.listen(1)
    print('Server listening on', server_address)

    while True:
        # 等待客户端连接
        client_socket, client_address = server_socket.accept()
        print('Received connection from', client_address)

        # 接收客户端消息
        message = client_socket.recv(1024)
        print(message.decode())

        # 发送响应消息到客户端
        response = 'Hello, client!'
        client_socket.send(response.encode())

        # 关闭连接
        client_socket.close()

if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="Simple client-server program")
    parser.add_argument("-t", "--type", required=True, choices=["client", "server"], help="The type of the program [client, server]")
    parser.add_argument("-i", "--ip", required=True, help="The IP address of the server")
    parser.add_argument("-p", "--port", required=True, type=int, help="The port number of the server")
    parser.add_argument("-m", "--message", help="The message to be sent to the server")

    args = parser.parse_args()

    if args.type == "client":
        if args.message:
            message = args.message
        else:
            message = "Hello, World!"
        client(args.ip, args.port, message)
    elif args.type == "server":
        server(args.ip, args.port)
```

在命令行工具中运行这个脚本时，需要使用`-t`参数指定程序类型（客户端还是服务器），使用`-i`参数指定服务器的IP地址，使用`-p`参数指定服务器的端口号，使用`-m`参数指定要发送的消息。如果不指定`-m`参数，则默认发送"Hello, World!"消息。

以下是一些示例命令：

启动服务器：

```
python script.py -t server -i 192.168.1.10 -p 9999
```

客户端发送消息：

```
python script.py -t client -i 192.168.1.10 -p 9999 -m "Hello, server!"
```

客户端发送默认消息：

```
python script.py -t client -i 192.168.1.10 -p 9999
```


#### 虚拟机三种网络设置
nat:能访问外网,但外网无法直接访问内部主机
仅主机模式:不能访问外网的nat模式,只能和主机进行通信
bridge    :和主机同一个网段,独立ip