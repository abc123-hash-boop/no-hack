# 阻止侵入您的服务器
## Python监听钓鱼模块
<img width="1111" height="624" alt="image" src="https://github.com/user-attachments/assets/ac746d65-d5f2-46dc-8615-7f772a26c01d" />
### 下载项目
运行:
``` bash
$ git clone https://github.com/abc123-hash-boop/no-hack.git
```
<img width="764" height="165" alt="image" src="https://github.com/user-attachments/assets/6a934160-a277-434c-85f4-2b51384376c6" />
### 启动服务器
运行:
``` bash
$ cd no-hack && bash start.sh
```
<img width="1824" height="808" alt="image" src="https://github.com/user-attachments/assets/7b22556c-56c7-4f78-aa85-4b2b2032e931" />

☠️禁止按ctrl+c，否则服务会挂，你可以试试systemd自启动服务，但是要以root身份打开start.sh，且目录要求设置正确

## iptables 配置模块

原理：检测到异常流量直接重定向到8080端口，触发钓鱼页面
放行ssh和本地回环
``` bash
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```
允许本地网络
``` bash
sudo iptables -A INPUT -s 192.168.0.0/16 -j ACCEPT
```
对外部放行端口
``` bash
sudo iptables -A INPUT -p tcp --dport <port> -j ACCEPT
```
fun
``` bash
sudo iptables -t nat -A PREROUTING -p tcp ! -s 192.168.0.0/16 -m multiport ! --dports 22,80,443 -j REDIRECT --to-port 80
```
防scan
``` bash
# 防止 Nmap 扫描
sudo iptables -A INPUT -p tcp --tcp-flags ALL NONE -j DROP   # NULL scan
sudo iptables -A INPUT -p tcp --tcp-flags ALL FIN -j DROP    # FIN scan
sudo iptables -A INPUT -p tcp --tcp-flags ALL FIN,PSH,URG -j DROP # Xmas scan
```
``` bash
# 允许 HTTP/HTTPS 和 整人
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 8080 -j ACCEPT
```
##效果展示
