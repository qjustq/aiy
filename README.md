# aiy配置过程记录

其他东西参考官网就好https://aiyprojects.withgoogle.com/voice

这里主要讲一下配置f q的过程

安装shadowsocks  
```
apt-get install python-pip  
pip install shadowsocks  
```

在任意位置创建一个json配置文件。
```
{
    "server":"server address",
    "server_port":server port,
    "local_address": "127.0.0.1",
    "local_port":local port,
    "password":"yourpassword",
    "timeout":300,
    "method":"aes-256-gcm",
    "fast_open": true
}
```

连接ss服务器  
```
sslocal -c json文件路径
```
配置启动自动连接  
编辑 ```/etc/rc.local``` 文件
```
sudo vi /etc/rc.local
```
在 ```exit 0``` 前面一行添加:  
```
sudo sslocal -c /etc/shadowsocks.json -d start
```

由于aiy的demo用的http协议通讯，而ss提供的是socket5方式，需要用polipo或类似软件将socket5转换成http方式  
安装polipo  
```
sudo apt-get install polipo
```
在任意位置创建config文件  
```
# This file only needs to list configuration variables that deviate
# from the default values.  See /usr/share/doc/polipo/examples/config.sample
# and "polipo -v" for variables you can tweak and further information.
logSyslog = true
logFile = /var/log/polipo/polipo.log
proxyAddress = "0.0.0.0"
proxyPort = 17070
socksParentProxy = "127.0.0.1:ss_local_port"
socksProxyType = socks5
allowedClients = 127.0.0.1
```

重启polipo  
```
sudo /etc/init.d/polipo restart
```

设置终端窗口http和https代理（关闭后无效）
```
export http_proxy="http://127.0.0.1:17070" && export https_proxy="http://127.0.0.1:17070"
```
取消代理
```
unset http_proxy && unset https_proxy
```

改配置文件使设置重启后仍然有  
```
nano ~/.bashrc
```
在最后添加
```
export http_proxy="http://127.0.0.1:17070"
export https_proxy="http://127.0.0.1:17070"
```

开始一个demo  
点击桌面上 start dev terminal  输入  
```
src/examples/voice/assistant_library_demo.py
```

