#### 1. 安装 jdk
    sudo add-apt-repository ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java7-installer
#### 2. 设置root密码
    sudo passwd root
#### 3. 安装依赖包
	sudo apt install -f
#### 4. 安装粘贴板工具
	sudo add-apt-repository ppa:diodon-team/stable
	sudo apt-get update
	sudo apt-get install diodon
#### 5. 安装 Unity Tweak Tool
    sudo add-apt-repository ppa:freyja-dev/unity-tweak-tool-daily
    sudo apt-get update
    sudo apt-get install unity-tweak-tool
#### 6. 安装oh my zsh
	1. git地址: https://github.com/robbyrussell/oh-my-zsh
#### 7. 安装开机自启动管理工具
	1.apt install sysv-rc-conf
	2.用空格来控制是否选中，x即代表选中
#### 8. history 显示时间
```bash
echo "export HISTTIMEFORMAT=\"%F %T \"" >> /etc/profile
```