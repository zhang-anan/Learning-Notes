## socket--网络通信
套接字是程序用于在本地或Internet上来回传递数据的通信通道的一个端点。套接字有两个主要的属性来控制它们发送数据的方式：地址家族控制使用OSI的网络层协议，和套接字类型控制传输层协议。
#### 1. 在网络上查找主机
socket 包括在网络上与域名服务交互的功能，这样程序就可以将服务器的主机名转换成其数字网络地址。在使用它们连接到服务器之前，应用程序不需要显式地转换地址，但是在报告错误时，它可能会很有用，包括数字地址和所使用的名称值。  
要找到当前主机的正式名称，请使用```gethostname()```
```python
import socket

print(socket.gethostname())
```
运行结果：返回的名称将取决于当前系统的网络设置，如果它位于不同的网络上（比如连接到无线LAN的笔记本），则可能会发生变化。
```
PC201508302009
```
使用```gethostbyname()```来查阅操作系统主机名解析API，并将服务器的名称转换为其数字地址。
```python
import socket

HOSTS = [
    'apu',
    'pymotw.com',
    'www.python.org',
    'nosuchname',
]

for host in HOSTS:
    try:
        print('{} : {}'.format(host, socket.gethostbyname(host)))
    except socket.error as msg:
        print('{} : {}'.format(host, msg))
```
运行结果：
```w
apu : [Errno 11004] getaddrinfo failed
pymotw.com : 66.33.211.242
www.python.org : 151.101.40.223
nosuchname : [Errno 11004] getaddrinfo failed
```
要获得关于服务器的更多命名信息，请使用gethostbynameex（）。它返回服务器的规范主机名、任何别名，以及所有可用的IP地址。
```python
import socket

HOSTS = [
    'apu',
    'pymotw.com',
    'www.python.org',
    'nosuchname',
]

for host in HOSTS:
    print(host)
    try:
        name, aliases, addresses = socket.gethostbyname_ex(host)
        print('  Hostname:', name)
        print('  Aliases :', aliases)
        print(' Addresses:', addresses)
    except socket.error as msg:
        print('ERROR:', msg)
    print()
```
运行结果：为服务器提供所有已知的IP地址，让客户端实现自己的负载平衡或故障转移算法。
```
apu
ERROR: [Errno 11004] getaddrinfo failed

pymotw.com
  Hostname: pymotw.com
  Aliases : []
 Addresses: ['66.33.211.242']

www.python.org
  Hostname: dualstack.python.map.fastly.net
  Aliases : ['www.python.org']
 Addresses: ['151.101.40.223']

nosuchname
ERROR: [Errno 11004] getaddrinfo failed
```
使用```getfqdn()```将局部名称转换为全称域名。
```python
import socket

for host in ['baidu', 'pymotw.com']:
    print('{:>10} : {}'.format(host, socket.getfqdn(host)))
```
运行结果：
```
     baidu : baidu
pymotw.com : apache2-zoo.george-washington.dreamhost.com
```
当服务器的地址可用时，使用```gethostbyaddr()```对名称进行反向查找。
```python
import socket

hostname, aliases, addresses = socket.gethostbyaddr('66.33.211.242')

print('Hostname :', hostname)
print('Aliases  :', aliases)
print('Addresses:', addresses)
```
运行结果：返回值是一个包含完整主机名、任何别名和与该名称相关联的所有IP地址的元组。
```
Hostname : apache2-zoo.george-washington.dreamhost.com
Aliases  : []
Addresses: ['66.33.211.242']
```
#### 2. 查找服务信息
除了IP地址之外，每个socket 地址还包括一个整数端口号。许多应用程序可以在同一个主机上运行，监听单个IP地址，但是一次只能使用一个socket ，可以在该地址使用端口。IP地址、协议和端口号的组合惟一地标识一个通信通道，并确保通过socket 发送的消息到达正确的目的地。
一些端口号是预先分配给特定协议的。例如，使用SMTP的电子邮件服务器之间的通信发生在使用TCP的端口25上，web客户端和服务器使用HTTP端口80。具有标准化名称的网络服务的端口号可以用```getservbyname()```查找。
```python
import socket
from urllib.parse import urlparse

URLS = [
    'http://www.python.org',
    'https://www.mybank.com',
    'ftp://prep.ai.mit.edu',
    'gopher://gopher.micro.umn.edu',
    'smtp://mail.example.com',
    'imap://mail.example.com',
    'imaps://mail.example.com',
    'pop3://pop.example.com',
    'pop3s://pop.example.com',
]

for url in URLS:
    parsed_url = urlparse(url)
    port = socket.getservbyname(parsed_url.scheme)
    print('{:>6} : {}'.format(parsed_url.scheme, port))

```
运行结果：尽管标准化的服务不太可能改变端口，但是在将来添加新服务时，使用系统调用而不是硬编码来查找值会更加灵活。
```
 http : 80
 https : 443
   ftp : 21
gopher : 70
  smtp : 25
  imap : 143
 imaps : 993
  pop3 : 110
 pop3s : 995
```
要反转服务端口查找，请使用```getservbyport()```。
```python
import socket
from urllib.parse import urlunparse

for port in [80, 443, 21, 70, 25, 143, 993, 110, 995]:
    url = '{}://example.com/'.format(socket.getservbyport(port))
    print(url)
```
反向查找对于从任意地址构建服务的url非常有用。  
运行结果：
```w
http://example.com/
https://example.com/
ftp://example.com/
gopher://example.com/
smtp://example.com/
imap://example.com/
imaps://example.com/
pop3://example.com/
pop3s://example.com/
```
#### 3. 回显服务器
Sockets 可以配置为充当服务器，监听传入消息，或作为客户机连接到其他应用程序。 TCP/IP socket连接之后通信是双向的。
这个样例程序基于标准库文档中的一个，接收传入的消息，并将它们返回给发送者。它首先创建一个TCP/IP套接字，然后```bind()```用于将套接字与服务器地址关联起来。在这种情况下，地址是localhost，指的是当前服务器，端口号是10000。
```python
import socket
import sys

# Create a TCP/IP socket
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Bind the socket to the port
server_address = ('localhost', 10000)
print('starting up on {} port {}'.format(*server_address))
sock.bind(server_address)

# Listen for incoming connections
sock.listen(1)

while True:
    # Wait for a connection
    print('waiting for a connection')
    connection, client_address = sock.accept()
    try:
        print('connection from', client_address)

        # Receive the data in small chunks and retransmit it
        while True:
            data = connection.recv(16)
            print('received {!r}'.format(data))
            if data:
                print('sending data back to the client')
                connection.sendall(data)
            else:
                print('no data from', client_address)
                break

    finally:
        # Clean up the connection
        connection.close()
```
调用```listen()```监听函数将socket进入服务器模式，```accept()```等待传入连接,整数参数是系统在拒绝新客户机之前应该在后台排队的连接数。这个例子只期望一次只处理一个连接。accept()返回服务器和客户端之间的开放连接，以及客户端的地址。连接实际上是另一个端口上不同的套接字（由内核分配）。数据是从recv（）的连接中读取的，并通过sendall（）传输。当与客户端进行通信完成时，需要使用close（）来清理连接。这个例子使用了一个尝试：finally块来确保关闭（）总是被调用，即使在发生错误的情况下也是如此。
浏览器访问localhost:10000, 运行结果：
```
starting up on localhost port 10000
waiting for a connection
connection from ('127.0.0.1', 5185)
received b'GET / HTTP/1.1\r\n'
sending data back to the client
received b'Host: localhost:'
sending data back to the client
received b'10000\r\nConnectio'
sending data back to the client
received b'n: keep-alive\r\nU'
sending data back to the client
received b'pgrade-Insecure-'
sending data back to the client
...
```
#### 4. 回显客户端
客户端程序以不同于服务器的方式设置其套接字。它不绑定到端口和监听，而是使用```connect()```将套接字直接连接到远程地址。
```python
import socket
import sys

# Create a TCP/IP socket
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Connect the socket to the port where the server is listening
server_address = ('localhost', 10000)
print('connecting to {} port {}'.format(*server_address))
sock.connect(server_address)

try:

    # Send data
    message = b'This is the message.  It will be repeated.'
    print('sending {!r}'.format(message))
    sock.sendall(message)

    # Look for the response
    amount_received = 0
    amount_expected = len(message)

    while amount_received < amount_expected:
        data = sock.recv(16)
        amount_received += len(data)
        print('received {!r}'.format(data))

finally:
    print('closing socket')
    sock.close()
```
运行结果：注意先把回显服务器运行起来。
客户端和服务器应该在单独的终端窗口中运行，这样它们就可以相互通信了。服务器输出显示传入的连接和数据，以及发回给客户端的响应。  
客户端：
```w
connecting to localhost port 10000
sending b'This is the message.  It will be repeated.'
received b'This is the mess'
received b'age.  It will be'
received b' repeated.'
closing socket
```
服务端：
```w
starting up on localhost port 10000
waiting for a connection
connection from ('127.0.0.1', 9153)
received b'This is the mess'
sending data back to the client
received b'age.  It will be'
sending data back to the client
received b' repeated.'
sending data back to the client
received b''
no data from ('127.0.0.1', 9153)
waiting for a connection
```
**客户端简单连法**：  
可用
```
sock = socket.create_connection(('localhost', 10000))
```
代替下面3行
```
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_address = ('localhost', 10000)
sock.connect(server_address)
```


