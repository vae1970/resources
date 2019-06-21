#### 通用ipv6配置
1. [https://github.com/killgcd/chromego/blob/master/ChromeGo/8.%E9%85%8D%E7%BD%AEIPv6.bat](https://github.com/killgcd/chromego/blob/master/ChromeGo/8.配置IPv6.bat)
2. 下载上面的bat文件，或者直接找代码中的server地址替换

#### windows ipv6配置

禁用系统网卡的ipv6协议！！！



启用iphelper服务

在cmd命令行（以管理员方式）中输入：

netsh int teredo set state default

netsh int 6to4 set state default

netsh int isatap set state default

netsh int ipv6 set teredo enterpriseclient

netsh int ter set state enterpriseclient

netsh int 6to4 set state enable 或者 netsh int 6to4 set state automatic


## 验证： 在cmd中输入netsh int ipv6 sh teredo 如果出现以下信息，即表示成功：

类型 : enterpriseclient
服务器名称 : win10.ipv6.microsoft.com.
客户端刷新间隔 : 30 秒
客户端端口 : unspecified
状态 : qualified 
客户端类型 : teredo client
网络 : managed
NAT : cone NAT 
特殊行为 : UPNP: 否，PortPreserving: 是
本地映射 : 啥啥啥啥啥哈
外部 NAT 映射 : 啥啥啥啥啥哈

如果teredo的状态是dormant，就是说teredo配置成功了，也连上了服务器，但没有激活

这一般来说是因为路由表的问题

在配置完teredo以后，会多出一个叫Teredo Tunneling Pseudo-Interface的适配器
这个在网络和共享中心中看不到，要用命令netsh int ipv6 sh interface才能看到

配置完teredo以后，这个适配器的状态应该是已连接
而通过命令netsh int ipv6 sh route查看路由表
应该有一条路由是这样的
否 手动 256 ::/0 55 Teredo Tunneling Pseudo-Interface
这表示ipv6的默认路由的下一跳是接口Teredo Tunneling Pseudo-Interface
没有这条，或者这条的下一跳接口不是这个，那么teredo的状态就会是dormant
加上这条就行
netsh int ipv6 add route ::/0 "Teredo Tunneling Pseudo-Interface"

剩下的就是在xx-net的配置，高级选项中，启用ipv6

------

关于win10升级到最新版1709以后，可能按照一般的路子配置完teredo以后仍然不好用
那么就在资源管理器中删掉Teredo Tunneling Pseudo-Interface，然后运行
netsh
int teredo
set state disabled
int ipv6 set teredo client
然后重启系统即可

参考资料：<https://answers.microsoft.com/en-us/windows/forum/windows_10-hardware/how-to-install-teredo-tunneling-pseudo-interface/62790531-c342-4c5b-b367-542784ae768f>



<https://github.com/XX-net/XX-Net/issues/7467>