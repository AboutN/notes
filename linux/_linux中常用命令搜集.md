### 信息查询
- find
```bash
# 可以按文件名, 时间, 文件大小, 文件类型, 权限及用户/组等信息查询文件
find / -name "filename" -type f
```

- getent 查询
```bash
getent passwd username
```

### 用户及权限操作
- useradd 添加用户
```bash
useradd 用户名  # 添加用户,同时创建用户目录和同名的用户组.
useradd -g 用户组名 用户名 # 添加用户,同时指定用户组. 该用户组必须是存在的.
```

- usermod 修改用户
```bash
# -g：修改用户的主组​（Primary Group）
usermod -g 新用户组名 用户名
# -G：修改用户的附加组​（Supplementary Groups）。​特别注意：使用 -G时若不加 -a选项，会覆盖用户原有的所有附加组。若要追加一个新组而不影响原有组，必须结合 -aG使用
usermod -aG 新用户组名 用户名 # 追加用户组
# 重要 要使修改生效需要重启.
```

- groupadd 添加用户组
```bash
groupadd 用户组名
```

- chown 修改所有者
```bash
chown -R username:groupname /path/to/file
```

- chmod 修改权限
```bash
chmod -R 755 /path/to/file # 使用数字模式设置权限
chmod -R u+x /path/to/file # 使用符号模式设置权限
```

#### 数字模式
使用三位八进制数字来表示权限，每一位数字对应一个用户类别（所有者、所属组、其他用户），每个数字是 `读`（4）、`写`（2）、`执行`（1）权限的和。
- `7` 表示 `rwx`（读、写、执行）。
- `6` 表示 `rw-`（读、写）。
- `5` 表示 `r-x`（读、执行）。
- `4` 表示 `r--`（读）。
- `3` 表示 `-wx`（写、执行）。
- `2` 表示 `-w-`（写）。
- `1` 表示 `--x`（执行）。
- `0` 表示 `---`（无权限）。
#### 符号模式
使用符号来指定权限的更改，常用符号如下：
- `u` 表示所有者（user）。
- `g` 表示所属组（group）。
- `o` 表示其他用户（others）。
- `a` 表示所有用户（all）。
- `+` 表示添加权限。
- `-` 表示移除权限。
- `=` 表示设置权限。
- `x` 表示执行权限。
- `r` 表示读权限。
- `w` 表示写权限。

### 网络相关
- ip 
- ss

### 进程及服务
- ps
- systemctl

### 软件包管理
- apt
``` bash
apt update # 更新软件包列表
apt install package_name # 安装软件包
apt remove package_name # 移除软件包
apt list --installed # 列出已安装的软件包
apt search package_name # 搜索软件包
apt show package_name # 显示软件包信息
apt upgrade # 升级已安装的软件包
```