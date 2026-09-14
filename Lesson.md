# Linux 用户与权限 

## 一、UID 关键数字

| UID | 身份 |
|-----|------|
| 0 | root |
| 1–999 | 系统账号 |
| ≥1000 | 普通用户 |

## 二、必背命令

### 用户管理
```bash
useradd        # 创建用户（底层，默认不建主目录）
adduser        # 创建用户（交互式，自动配置）
userdel -r     # 删除用户 + 主目录(不带-r就不删除主目录)
usermod -aG    # 添加附加组（-a 不能忘）
usermod -d -m  # 迁移主目录
```

### 密码管理

```bash
passwd         # 改密码
passwd -l      # 锁定账号
passwd -u      # 解锁账号
```

### 组管理

```bash
groupadd       # 创建组
groupdel       # 删除组
```
### 提权

```bash
sudo           # 临时提权
visudo         # 编辑 sudoers 文件
```

### 审计

```bash
lastlog        # 最近登录
id             # 查看用户身份
sudo -l        # 查看当前用户 sudo 权限
```


## 三、关键文件
```bash
/etc/passwd 用户信息
/etc/shadow 密码哈希，仅 root 可读
/etc/group 组信息
/etc/sudoers sudo 授权配置
```

## 四、提示符
```bash
# root   $ 普通用户
```

## 五、退出方式
```bash
exit 最常用
logout 清理进程和临时文件
Ctrl+D 发送 EOF
```

## 六、易错点
```bash
usermod -aG -a 忘写会覆盖原有附加组
userdel vs userdel -r 后者连主目录一起删
useradd vs adduser 前者底层，后者交互
创建用户报“文件已存在” 删 /var/spool/mail/用户名
创建用户报“组不存在” 先 groupadd
无法登录 Shell 被设 nologin 或账号被锁
```
