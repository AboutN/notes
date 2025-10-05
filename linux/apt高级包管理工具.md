# APT (Advanced Package Tool) 高级包管理工具
apt 是新的面向用户更友好的包管理工具，而老的apt-get, apt-cache, apt-config等命令也并没有完全从系统中删除, 他们共享相同的底层库和基础设施。 在功能上可能会有些相互重叠。

``` text
用户界面层：
├── apt (新一代命令行工具)
├── apt-get (传统命令行工具) 
├── apt-cache (缓存查询工具)
└── aptitude (高级交互式工具)
    ↓
底层核心层：
├── libapt-pkg (核心库)
├── libapt-inst (安装库)
└── dpkg (底层包管理器)
```
apt 和 apt-get 都依赖 libapt-pkg 这个核心库, 它们不互相依赖，而是共享相同的底层基础设施, 真正的底层包管理器是 dpkg. 

dpkg 是 Debian 及其衍生系统（如 Ubuntu、Debian、Linux Mint 等）中用于管理 .deb 格式软件包的核心工具，主要用于直接操作本地软件包文件. 


ubuntu 中apt的配置信息主要在 `etc/apt/` 目录下. 


## 主要功能
1. 自动处理依赖关系：在安装或更新软件时，自动下载并安装所需的其它软件包。
2. 从软件源（Repository）获取软件：从一个预设的、集中的服务器列表（即“源”）下载软件包和更新信息。
3. 提供搜索、安装、升级和卸载软件 的统一接口。

## apt配置目录中各文件及目录的含义
``` text
/etc/apt/
├── sources.list                 # 主软件源列表
├── sources.list.d/              # 附加软件源目录
├── apt.conf.d/                  # APT 配置片段
├── preferences.d/               # 软件包优先级配置
├── trusted.gpg.d/               # 可信密钥目录
├── auth.conf.d/                 # 认证配置
├── keyrings/                    # 密钥环文件（新版）
└── [其他文件...]
```
#### sources.list 
该文件定义系统从哪些服务器获取软件包。

文件格式及含义：
``` text
deb http://archive.ubuntu.com/ubuntu/ jammy main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu/ jammy-updates main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu/ jammy main restricted universe multiverse
deb [signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu jammy stable
```
格式说明: 
- deb：二进制软件包仓库
- deb-src：源代码软件包仓库
- URL：仓库服务器地址
- 发行版代号：如 jammy (22.04), focal (20.04)
- 组件：main (官方支持), restricted (专利软件), universe (社区维护), multiverse (非自由软件)
- 选项字段: 方括号中的内容为选项字段, 可以添加多个, 每个选项字段通过空格隔开, 下面列出几个常用的选项
    - arch: 指定适用的CPU架构, 如: [arch=amd64,arm64]
    - signed-by: 指定密钥文件, 如: [signed-by=/etc/apt/keyrings/docker.gpg]
    - trusted: 信任源,无需验证, 如: [trusted=yes]
    - lang: 语言选项, 如: [lang=en_US,zh_CN]


#### sources.list.d/
该目录中存放第三方软件的独立软件源文件，避免污染主配置文件。

文件名一般如下
``` text
/etc/apt/sources.list.d/
├── docker.list
├── google-chrome.list
├── vscode.list
└── nodesource.list
```
文件格式同sources.list的格式一致.

#### keyrings/
新版本的 APT 推荐将密钥放在这个目录，并在 sources.list 中显式引用。

`trusted.gpg.d/` 目录旧版全局信任方法, 在这里存放的密钥会被所有软件源信任. 所以会存在一个被入侵的源密钥会影响整个系统。keyrings 目录中的每个密钥只被特定的软件源使用

``` text
# 在 sources.list 中引用
deb [signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu jammy stable
```
密钥文件分为两种 
- .gpg 为二进制格式
- .asc 为文本格式

`/usr/share/keyrings/` 目录存放的是系统默认的密钥

下面是docker官方源的安装, 可用于理解上的理念
``` bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
# 防止keyring权限问题
sudo install -m 0755 -d /etc/apt/keyrings
# 下载密钥, 并保存在keyyring目录下, 这里密钥为文本格式 .asc
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# 在sources.list.d目录中添加docker源, 并在源的前面加上[signed-by=/etc/apt/keyrings/docker.asc], 指定验证密钥
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

## apt常用命令
1. apt update: 更新软件源列表
2. apt upgrade: 升级已安装的软件包
3. apt install: 安装软件包
4. apt remove: 删除软件包
5. apt purge: 删除软件包及其配置文件
6. apt search <关键词>: 搜索软件包
``` bash
apt search --names-only <关键词> # 只在名称中搜索
```
7. apt show <软件包名>: 显示软件包信息
8. apt list: 列出可用的软件包
``` bash
apt list --installed #  列出已安装的软件包
```
9. apt depend: 显示软件包的依赖关系
10. apt autoremove: 删除不再需要的依赖包

