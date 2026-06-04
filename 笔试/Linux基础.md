# Linux 基础

## 一、基本概念

### 1.1 内核架构

Linux 内核是**宏内核 (Monolithic Kernel)**，所有核心功能（进程管理、内存管理、文件系统、设备驱动、网络协议栈）都运行在内核空间。

与微内核对比：宏内核性能好但稳定性/可维护性稍差；微内核（如 Minix、QNX）相反。

### 1.2 文件系统层次标准 (FHS)

| 目录 | 用途 |
|------|------|
| `/bin` | 基本用户命令（ls, cp, mv） |
| `/sbin` | 系统管理命令（fdisk, iptables） |
| `/etc` | 配置文件 |
| `/home` | 用户主目录 |
| `/root` | root 用户主目录 |
| `/var` | 可变数据（日志、缓存、邮件） |
| `/tmp` | 临时文件 |
| `/usr` | 用户程序和数据 |
| `/usr/bin` | 用户命令 |
| `/usr/lib` | 库文件 |
| `/usr/local` | 本地安装的软件 |
| `/opt` | 第三方软件 |
| `/dev` | 设备文件 |
| `/proc` | 进程信息虚拟文件系统 |
| `/sys` | 设备和驱动信息虚拟文件系统 |
| `/boot` | 启动文件（内核、引导加载器） |
| `/mnt`, `/media` | 挂载点 |

### 1.3 一切皆文件

Linux 将所有资源抽象为文件：普通文件、目录、设备（块设备/字符设备）、管道、Socket、符号链接等，统一通过文件描述符 (fd) 操作。

## 二、常用命令

### 2.1 文件操作

```bash
ls -la                  # 列出所有文件（含隐藏），长格式
ls -lh                  # 人类可读的文件大小
cd /path/to/dir         # 切换目录
pwd                     # 显示当前目录
cp -r src dst           # 递归复制目录
mv old new              # 移动/重命名
rm -rf dir              # 递归强制删除（慎用！）
mkdir -p a/b/c          # 递归创建目录
touch file              # 创建空文件或更新时间戳
ln -s target link       # 创建软链接
ln target link          # 创建硬链接
find . -name "*.log"    # 按名字查找文件
find . -size +100M      # 按大小查找
find . -mtime -7        # 7天内修改的文件
find . -type f -exec grep -l "pattern" {} \;  # 查找包含内容的文件
```

### 2.2 文本处理

```bash
cat file                # 显示文件内容
head -n 20 file         # 前20行
tail -n 20 file         # 后20行
tail -f file            # 实时跟踪文件末尾（看日志）
grep -rn "pattern" .    # 递归搜索并显示行号
grep -i "pattern"       # 忽略大小写
grep -v "pattern"       # 排除匹配行
grep -E "a|b"           # 扩展正则（或用 egrep）

wc -l file              # 行数
wc -w file              # 单词数

sort file               # 排序
sort -n                 # 数字排序
sort -r                 # 逆序
sort -k 2               # 按第2列排序

uniq                    # 去除相邻重复行（通常配合 sort）
uniq -c                 # 统计重复次数

# awk：按列处理
awk '{print $1, $3}' file          # 打印第1、3列
awk -F: '{print $1}' /etc/passwd   # 指定分隔符
awk '$3 > 100 {print $1}' file     # 条件过滤

# sed：流编辑器
sed 's/old/new/g' file             # 全局替换
sed -n '5,10p' file                # 打印5-10行
sed -i 's/old/new/g' file         # 原地修改

# 管道组合示例
cat access.log | grep "404" | awk '{print $1}' | sort | uniq -c | sort -rn | head -10
```

### 2.3 进程管理

```bash
ps aux                  # 所有进程详细信息
ps -ef                  # 另一种格式
top                     # 实时进程监控
htop                    # 更友好的 top（需安装）

kill PID                # 发送 SIGTERM
kill -9 PID             # 发送 SIGKILL（强制杀死）
kill -HUP PID           # 重新加载配置

nohup cmd &             # 后台运行，不受终端关闭影响
jobs                    # 查看后台任务
fg %1                   # 将后台任务调到前台
bg %1                   # 继续后台运行

# 优先级
nice -n 10 cmd          # 以较低优先级运行
renice -n 5 -p PID      # 修改运行中进程的优先级
```

### 2.4 网络工具

```bash
ping host               # 测试连通性
traceroute host         # 追踪路由
curl -v URL             # 发送 HTTP 请求（-v 显示详情）
wget URL                # 下载文件
ifconfig / ip addr      # 查看网络接口
ip route                # 查看路由表

netstat -tlnp           # 查看监听端口（TCP）
ss -tlnp                # 更快的 netstat 替代

# 端口占用
lsof -i :8080           # 查看占用8080端口的进程

# DNS
nslookup domain         # DNS 查询
dig domain              # 更详细的 DNS 查询
```

### 2.5 压缩与归档

```bash
tar -czf archive.tar.gz dir/   # 打包并 gzip 压缩
tar -xzf archive.tar.gz        # 解压 gzip
tar -cjf archive.tar.bz2 dir/  # bzip2 压缩
tar -xjf archive.tar.bz2       # 解压 bzip2
tar -tf archive.tar.gz         # 查看内容

zip -r archive.zip dir/        # zip 压缩
unzip archive.zip               # 解压 zip

gzip file                       # 压缩单个文件
gunzip file.gz                  # 解压
```

### 2.6 磁盘

```bash
df -h                   # 查看磁盘使用（文件系统级别）
du -sh dir/             # 查看目录大小
du -h --max-depth=1     # 一级子目录大小

mount /dev/sdb1 /mnt    # 挂载
umount /mnt             # 卸载
fdisk -l                # 查看磁盘分区
lsblk                   # 查看块设备
```

## 三、权限系统

### 3.1 用户与组

```bash
whoami                  # 当前用户
id                      # 当前用户 UID/GID
useradd username        # 创建用户
passwd username         # 修改密码
usermod -aG group user  # 将用户加入组
groupadd groupname      # 创建组
```

### 3.2 文件权限

`ls -l` 输出格式：`-rwxr-xr-- 1 owner group size date filename`

```
     owner  group  others
  r  w  x | r  w  x | r  w  x
  4  2  1 | 4  2  1 | 4  2  1
```

```bash
chmod 755 file          # rwxr-xr-x
chmod u+x file          # 给所有者加执行权限
chmod go-w file         # 去掉组和其他人的写权限
chown user:group file   # 修改所有者和组
chown -R user:group dir # 递归修改
```

### 3.3 特殊权限

| 权限 | 数字 | 含义 |
|------|------|------|
| SUID | 4000 | 执行时以文件所有者身份运行（如 /usr/bin/passwd） |
| SGID | 2000 | 执行时以文件所属组身份运行；目录下新建文件继承组 |
| Sticky Bit | 1000 | 目录中的文件只有所有者能删除（如 /tmp） |

```bash
chmod 4755 file         # 设置 SUID
chmod +t dir            # 设置 Sticky Bit
```

### 3.4 umask

umask 控制新建文件/目录的默认权限。
- 文件默认权限 = 0666 - umask
- 目录默认权限 = 0777 - umask
- 常见 umask = 0022 → 文件 644，目录 755

## 四、Shell 编程

### 4.1 变量

```bash
name="hello"            # 定义变量（等号两边无空格）
echo $name              # 使用变量
echo ${name}_world      # 明确边界
readonly name           # 只读变量
unset name              # 删除变量

# 特殊变量
$0    # 脚本名
$1-$9 # 位置参数
$#    # 参数个数
$@    # 所有参数（独立字符串）
$*    # 所有参数（单个字符串）
$?    # 上一个命令的返回值（0成功）
$$    # 当前 PID
```

### 4.2 条件判断

```bash
if [ condition ]; then
    cmd
elif [ condition ]; then
    cmd
else
    cmd
fi

# 常用条件
[ -f file ]     # 文件存在且为普通文件
[ -d dir ]      # 目录存在
[ -e path ]     # 路径存在
[ -z "$str" ]   # 字符串为空
[ -n "$str" ]   # 字符串非空
[ "$a" = "$b" ] # 字符串相等
[ $a -eq $b ]   # 数字相等
[ $a -gt $b ]   # 大于
[ $a -lt $b ]   # 小于
```

### 4.3 循环

```bash
# for 循环
for i in 1 2 3 4 5; do echo $i; done
for i in $(seq 1 10); do echo $i; done
for file in *.txt; do echo $file; done
for ((i=0; i<10; i++)); do echo $i; done

# while 循环
while [ condition ]; do
    cmd
done

# 读取文件每一行
while IFS= read -r line; do
    echo "$line"
done < file.txt
```

### 4.4 函数

```bash
function greet() {
    echo "Hello, $1"
    return 0
}
greet "World"
result=$?       # 获取返回值
```

### 4.5 管道与重定向

```bash
cmd > file      # 标准输出重定向到文件（覆盖）
cmd >> file     # 追加
cmd 2> file     # 标准错误重定向
cmd &> file     # 标准输出和错误都重定向
cmd < file      # 从文件读取输入
cmd1 | cmd2     # 管道，cmd1 的输出作为 cmd2 的输入

# /dev/null：黑洞，丢弃所有输出
cmd > /dev/null 2>&1
```

### 4.6 正则表达式基础

| 符号 | 含义 |
|------|------|
| `.` | 匹配任意单个字符 |
| `*` | 前一个字符重复 0 次或多次 |
| `+` | 前一个字符重复 1 次或多次（扩展正则） |
| `?` | 前一个字符重复 0 次或 1 次（扩展正则） |
| `^` | 行首 |
| `$` | 行尾 |
| `[]` | 字符集 |
| `[^]` | 排除字符集 |
| `\d` | 数字（部分工具支持） |
| `\w` | 单词字符 |
| `{n,m}` | 重复 n 到 m 次 |

## 五、进程管理

### 5.1 进程与线程模型

- Linux 用 `task_struct` 描述进程/线程
- `fork()`：创建子进程，复制父进程（Copy-On-Write）
- `exec()`：用新程序替换当前进程的代码和数据
- `fork() + exec()` 是创建新进程的标准方式
- `wait()` / `waitpid()`：父进程等待子进程结束

**孤儿进程**：父进程先终止，子进程被 init (PID=1) 收养
**僵尸进程**：子进程终止但父进程未调用 wait()，进程表项残留。解决：让父进程处理 SIGCHLD。

### 5.2 信号机制

| 信号 | 编号 | 默认行为 | 含义 |
|------|------|----------|------|
| SIGHUP | 1 | 终止 | 终端挂断 |
| SIGINT | 2 | 终止 | Ctrl+C |
| SIGQUIT | 3 | 终止+core dump | Ctrl+\ |
| SIGKILL | 9 | 终止 | 强制杀死（不可捕获） |
| SIGSEGV | 11 | 终止+core dump | 段错误 |
| SIGTERM | 15 | 终止 | 优雅终止（默认 kill） |
| SIGSTOP | 19 | 暂停 | 暂停进程（不可捕获） |
| SIGCONT | 18 | 继续 | 恢复暂停的进程 |

### 5.3 守护进程 (Daemon)

在后台运行的服务进程，不关联终端。创建步骤：fork → 父进程退出 → setsid() 创建新会话 → 关闭文件描述符 → 改变工作目录。

现代 Linux 使用 systemd 管理服务：
```bash
systemctl start/stop/restart/status service
systemctl enable/disable service    # 开机自启
```

### 5.4 cron 定时任务

```bash
crontab -e              # 编辑当前用户的 cron
crontab -l              # 查看

# 格式：分 时 日 月 周 命令
# 每天凌晨2点执行备份
0 2 * * * /usr/local/bin/backup.sh
# 每5分钟执行
*/5 * * * * /path/to/script.sh
```

## 六、文件系统

### 6.1 inode

每个文件对应一个 inode，存储文件的元数据（权限、大小、时间戳、数据块指针等），**不存储文件名**。文件名存储在目录项中，目录项将文件名映射到 inode 号。

```bash
ls -i file              # 查看 inode 号
stat file               # 查看 inode 详细信息
df -i                   # 查看 inode 使用情况
```

### 6.2 硬链接与软链接

| 对比 | 硬链接 | 软链接（符号链接） |
|------|--------|-------------------|
| 本质 | 多个目录项指向同一个 inode | 一个独立文件，内容是目标路径 |
| 跨文件系统 | 不可以 | 可以 |
| 链接目录 | 不可以 | 可以 |
| 删除原文件 | 仍可访问（inode 引用计数>0） | 链接失效（悬空链接） |
| inode | 相同 | 不同 |

### 6.3 常见文件系统

| 文件系统 | 特点 |
|----------|------|
| ext4 | Linux 默认，日志文件系统，支持大文件 |
| xfs | 高性能，擅长大文件和并行 I/O |
| btrfs | 支持快照、压缩、子卷 |
| tmpfs | 内存文件系统，速度极快 |

### 6.4 /proc 与 /sys

- `/proc`：虚拟文件系统，提供内核和进程信息
  - `/proc/cpuinfo`：CPU 信息
  - `/proc/meminfo`：内存信息
  - `/proc/PID/`：特定进程的信息
  - `/proc/PID/fd/`：文件描述符
- `/sys`：设备和驱动信息，sysfs 文件系统

## 七、网络配置

### 7.1 SSH

```bash
ssh user@host           # 远程登录
ssh -p 2222 user@host   # 指定端口
ssh-keygen -t rsa       # 生成密钥对
ssh-copy-id user@host   # 复制公钥到远程主机
scp file user@host:/path   # 安全复制文件
```

### 7.2 防火墙

```bash
# iptables（传统）
iptables -L             # 查看规则
iptables -A INPUT -p tcp --dport 80 -j ACCEPT   # 允许80端口
iptables -A INPUT -j DROP                        # 默认拒绝

# firewalld（CentOS 7+）
firewall-cmd --list-all
firewall-cmd --add-port=80/tcp --permanent
firewall-cmd --reload
```

## 八、包管理

| 系统 | 包管理器 | 常用命令 |
|------|----------|----------|
| Debian/Ubuntu | apt | `apt update`, `apt install pkg`, `apt remove pkg`, `apt search pkg` |
| RHEL/CentOS | yum/dnf | `yum install pkg`, `yum update`, `yum search pkg` |

## 九、常见面试题

**Q: 如何查看一个端口被哪个进程占用？**
```bash
lsof -i :PORT
# 或
ss -tlnp | grep PORT
# 或
netstat -tlnp | grep PORT
```

**Q: 如何查找当前目录下最大的 10 个文件？**
```bash
find . -type f -exec du -h {} + | sort -rh | head -10
```

**Q: 如何实时查看日志文件？**
```bash
tail -f /var/log/syslog
```

**Q: 如何统计一个文件中某个单词出现的次数？**
```bash
grep -c "word" file      # 匹配的行数
grep -o "word" file | wc -l   # 出现的次数
```

**Q: 硬链接和软链接的区别？**
见 6.2 节。核心：硬链接共享 inode，软链接是独立文件存储路径。

**Q: 什么是 inode？一个文件系统 inode 用完了但磁盘空间还有怎么办？**
inode 存储文件元数据。inode 用完则无法创建新文件。解决：删除不必要的小文件（尤其是空文件），或重新格式化增大 inode 数。

**Q: fork() 返回值是什么？**
- 父进程中返回子进程的 PID（>0）
- 子进程中返回 0
- 失败返回 -1

**Q: 如何让一个进程在后台运行且不受终端关闭影响？**
```bash
nohup command &          # 方法1
# 或使用 screen/tmux     # 方法2
```
