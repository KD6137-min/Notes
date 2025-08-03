SSH（Secure Shell），加密网络协议，用于在不安全的网络中安全地远程登录和管理服务器，以及进行文件传输等操作

### 安装配置

```bash
sudo pacman -Syyu
sudo pacman -S openssh

# 启用
sudo systemctl start sshd
sudo systemctl enable sshd		# 设置开机自启
sudo systemctl status sshd 		# 检查服务状态

# 配置
vim /etc/ssh/sshd_config

sudo systemctl restart sshd 	# 重启服务使配置生效
```



### **基本连接命令**

- 连接到远程服务器：首次连接时会提示确认主机指纹，输入`yes`后继续

  ```bash
  ssh username@hostname_or_ip
  ```

- 指定端口连接：`ssh -p 2222 user@192.168.1.100`，默认端口为22

- 测试连接：`ssh -T <server>`

- 查看日志：`ssh -v`

### **认证方式**

- 密钥认证（免密码登录）

  - 生成密钥对：`ssh-keygen -t rsa -b 4096`
  - 将公钥复制到远程服务器：`ssh-copy-id user@192.168.1.100`，或手动将公钥内容添加到远程服务器的`~/.ssh/authorized_keys`文件中
- 使用指定私钥连接：`ssh -i ~/.ssh/id_rsa_private user@192.168.1.100`

### **执行远程命令**

- 单条命令

  ```
  ssh user@192.168.1.100 "ls -l"
  ```

- 多条命令

  ```
  ssh user@192.168.1.100 "command1; command2"
  ```

- 交互式终端（如运行`vim`）：使用`-t`选项：

  ```
  ssh -t user@192.168.1.100 "vim /path/to/file"
  ```

### **文件传输**

- SCP命令

  - 从本地复制到远程：

    ```
    scp /local/file.txt user@192.168.1.100:/remote/path/
    ```

  - 从远程复制到本地：

    ```
    scp user@192.168.1.100:/remote/file.txt /local/path/
    ```

  - 递归复制目录：`-r`选项

    ```
    scp -r /local/dir/ user@192.168.1.100:/remote/path/
    ```

- SFTP交互式传输

  ```
  sftp user@192.168.1.100
  ```

  支持`put`（上传）、`get`（下载）等命令

### **端口转发与隧道**

- 本地端口转发：将远程主机的3306端口映射到本地的3333端口：访问本地的3333端口即访问远程MySQL服务

  ```
  ssh -L 3333:localhost:3306 user@192.168.1.100
  ```

- 动态端口转发（SOCKS代理）：通过本地8080端口代理网络流量

  ```
  ssh -D 8080 user@192.168.1.100
  ```

### **配置与优化**

- **SSH客户端配置**：编辑`~/.ssh/config`文件简化连接，之后可直接用`ssh myserver`连接

  > 注意ssh配置文件名称为config，无扩展名

  ```
  Host myserver
      HostName 192.168.1.100
      User user
      Port 2222
      IdentityFile ~/.ssh/id_rsa
  ```

- 防止连接超时：在`~/.ssh/config`中添加：

  ```
  Host *
      ServerAliveInterval 60
      ServerAliveCountMax 3
  ```

### **安全建议**

1. **禁用root登录**：修改`/etc/ssh/sshd_config`，设置`PermitRootLogin no`
2. **使用强密码或密钥**：避免弱密码认证
3. **限制访问IP**：通过防火墙规则限制SSH端口访问

