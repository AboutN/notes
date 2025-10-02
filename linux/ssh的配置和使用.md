# SSH的配置和使用
当使用ssh的密钥进行身份验证时,需要把公钥添加到远程服务器的authorized_keys文件中.

## 背景知识
SSH(Secure Shell)是一种网络协议，用于远程登录和控制远程计算机。

OpenSSH是SSH协议的一个实现，包含SSH协议相关的工具。如ssh、ssh-keygen、scp、sftp等

## 服务器端
### 安装
**linux**: `sudo apt install openssh-server`

### 配置文件存放位置
1. sshd_config: SSH服务端的主要配置文件 
    - Linux系统: 通常位于 /etc/ssh/sshd_config 
    - Windows系统: 通常位于 %programdata%\ssh\sshd_config   
2. 其他重要文件: 
    - authorized_keys: 存放客户端公钥的文件，用于用户身份验证. 位于用户主目录下的 .ssh/authorized_keys
    - known_hosts: 存放已知主机的公钥文件

authorized_keys 文件采用纯文本格式，每行包含一个公钥。文件的基本结构如下：
```
# 注释行以 # 开头
ssh-rsa AAAAB3NzaC1yc2E... 用户标识注释
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5... 用户标识注释
ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTY... deploy@github
```


## 客户端

### 安装
**windows**: win10系统, 点击 设置->系统->可选功能->添加功能->OpenSSH 客户端 安装

**linux**: 一般系统默认自带, 如果没有可以通过 `sudo apt install openssh-client`

### 配置文件存放位置
linux 和 windows 的配置文件存放位置都是在用户目录下的.ssh目录下

### 目录中常见文件及含义
**config**: SSH 客户端的配置文件，用于定义主机别名、端口、密钥路径等个性化设置。

**known_hosts**: 记录了已经验证过的远程主机的公钥，用于验证远程主机的合法性。

**id_rsa**: SSH 密钥对中的私钥文件，用于验证用户身份。

**id_rsa.pub**: SSH 密钥对中的公钥文件，用于远程主机验证用户身份。

**其他类型的文件**: 其他类型的密钥文件，如 id_ecdsa、id_ed25519 等，用于不同的密钥算法。


### 配置文件详解
**Host**: 定义主机别名，方便后续使用。

**HostName**: 定义远程主机的地址，可以是IP地址或域名。

**Port**: 定义远程主机的SSH端口号，默认是22端口。

**User**: 定义登录远程主机的用户名。

**IdentityFile**: 定义用于验证用户身份的私钥文件路径。

基本格式
``` ssh
# 注释以 # 开头
Host 别名                                   # 定义主机别名（用于命令行）
  HostName 实际主机名或IP                   # 真实连接目标
  User 用户名                               # 登录用户
  Port 端口号                               # 非默认端口时指定（默认 22）
  IdentityFile 私钥路径                     # 指定私钥文件
  IdentitiesOnly yes                        # 强制仅使用指定密钥
  ProxyCommand 代理命令                     # 通过代理连接
  ForwardAgent yes                          # 启用 SSH 代理转发
  LocalForward 本地端口:目标主机:目标端口   # 本地端口转发
  RemoteForward 远程端口:本地主机:本地端口  # 远程端口转发
```
使用配置文件的好处是可以使用别名代替完整的输入
``` bash
ssh myserver  # 等同于 ssh -p 22 root@192.168.1.100
```

### 基础命令的用法
**ssh**: 用于登录远程主机。
``` bash
ssh [选项] [用户名@]主机名
# 例如：ssh root@192.168.1.100
```
- username: 登录远程主机的用户名。
- hostname: 远程主机的地址，可以是IP地址或域名。
- -i 密钥文件 : 指定用于身份验证的私钥文件
    - 默认使用 ~/.ssh/id_rsa
    - 例如：ssh -i ~/.ssh/my_key user@hostname
- -p 端口 : 指定远程主机的SSH端口，默认是22端口。
- -v: 显示详细日志。

**ssh-keygen**: 用于生成SSH密钥对。
``` bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
# 例如：ssh-keygen -t rsa -b 4096 -C "root@192.168.1.100"
```
- -t: 指定生成的密钥类型
    - rsa: 生成RSA类型的密钥（最常用） 
    - ecdsa: 生成ECDSA类型的密钥 
    - ed25519: 生成Ed25519类型的密钥
- -b: 指定密钥的位数
    - 2048: 生成2048位密钥（默认值） 
    - 4096: 生成4096位密钥（更安全，但计算量稍大）
- -C: 指定密钥的注释,填写一个描述密钥的注释，方便后续管理。
- -f: 指定密钥文件的路径和名称，默认为id_rsa。

