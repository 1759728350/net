是一种应用层的通信协议,基于TCP  
采用RSA非对称加密  
所以安全性较之于FTP,Telnet安全性要高  
每次传输之前都会对数据用公钥和私钥进行加密解密  

SSH只是一种协议,想要使用还是要用实现其协议的软件,比如openSSH  
然后启动监听端口


##### 用途
SSH(secure shell)  
用于远程登录服务器,远程登录到服务器后  
我们就可以对服务器使用命令  
我们也可以接收到服务器反馈的命令执行结果字符  
从shell这个词我们就不难看出  
该协议常用于命令行远程传输命令和反馈结果  

##### 使用
