# Linux 命令练习 · 20个常用命令速查


## 一、用户与系统信息

### 1. who
- **作用**：查看当前登录系统的用户信息
- **参数**：`-a` 显示全部信息
```bash
  who -a
```

### 2. whoami

- **作用**：显示当前终端登录的用户名

### 3.uname

- 作用：显示系统信息
- 参数：-a 全部信息，-r 仅内核版本
 ```bash
uname -a
```

## 二、时间与日历

### 4. date

- 作用：显示或设置系统时间
- 参数：+%FORMAT 自定义格式
```bash
  date +"%Y-%m-%d %H:%M"
  date +"%F"
  sudo date --set="2026-09-09 17:30:00"
```

### 5. cal

- 作用：显示日历
- 参数：-3 上月/本月/下月，-y 全年
 ```bash
  cal 11 2022
  cal -y
  ```


## 三、文件与目录操作

### 6. cd

- 作用：切换工作目录
```bash
  cd /tmp        # 进入/tmp
  cd ~           # 返回家目录
  cd -           # 返回上一个目录
```

### 7. pwd

- 作用：显示当前工作目录路径
```bash
  pwd
```

### 8. ls

- 作用：列出目录内容
- 参数：-l 详细列表，-a 显示隐藏文件，-h 易读大小
```bash
  ls -alh
```

### 9. cp

- 作用：复制文件/目录
- 参数：-r 递归复制目录，-i 覆盖前提示
```bash
  cp file1 file2
  cp -r dir1 dir2
```

### 10. mv

- 作用：移动/重命名文件或目录
```bash
  mv old.txt new.txt        # 重命名
  mv file1 ~/Documents/     # 移动
```

### 11. rm

- 作用：删除文件/目录
- 参数：-r 递归删除目录，-f 强制删除
```bash
  rm file.txt
  rm -r mydir
```

### 12. mkdir & rmdir

- 作用：创建/删除目录
- 参数：mkdir -p 自动创建父目录
```bash
  mkdir project
  mkdir -p a/b/c
  rmdir empty_dir
```


## 四、文件内容查看与编辑

### 13. cat

- 作用：显示/合并/创建文件
```bash
  cat file.txt
  cat > newfile.txt              # 键盘输入创建，Ctrl+D 结束
  cat file1 file2 > combined.txt # 合并文件
```



### 14.head & tail

- 作用：显示文件头部/尾部内容
- 参数：-n NUM 指定行数，tail -f 实时追踪
```bash
  head -n 5 log.txt
  tail -f /var/log/syslog
```

### 15. more

- 作用：分页查看大文件（空格翻页，Q退出）   
 ```bash
  more /etc/ssh/sshd_config
```

## 五、其他实用命令

### 16. echo

- 作用：输出文本或变量值
```bash
  echo "Hello World"
  echo $PATH
```

### 17. clear

- 作用：清空终端屏幕（等价 Ctrl+L）

### 18.  passwd

- 作用：修改当前用户密码
- 注意：root 可修改任意用户密码
```bash
  passwd
  sudo passwd username
```

### 19. bc & dc

- 作用：计算器工具
```bash
  echo "5 * (10 + 3)" | bc    # 输出65
  dc -e "5 3 * p"             # 输出15
```

### 20. man

- 作用：查看命令手册（按 Q 退出）
```bash
  man ls
  ```

## 六、关键注意事项

1. 权限控制：系统级操作需加 sudo
2. 危险命令：rm -rf / 会删除整个系统，切勿尝试；建议用 rm -i    
3. 安装缺失工具：sudo apt install ncal dc
