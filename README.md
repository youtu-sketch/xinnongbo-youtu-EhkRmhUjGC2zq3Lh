
## 前言


大学的时候也写过和`Ingram`差不多的工具，不过那时候已经玩到没有兴致了，代码已不知道哪里去。没想到在Github看到了这个工具，实现思路和我的几乎一样，互联网就是这么神奇。


Ingram的Github：[https://github.com/jorhelp/Ingram](https://github.com)


本着不能让服务器闲着的想法，开始吧


## 安装`Ingram`



```
git clone https://github.com/jorhelp/Ingram.git
cd Ingram
pip3 install virtualenv
python3 -m virtualenv venv
source venv/bin/activate
pip3 install -r requirements.txt

```

安装后需要一个目标文件，这里用`masscan`来搞一波


## 安装`masscan`


安装`masscan`的坑不少，有报错丢gpt就好了


安装步骤



```
sudo yum groupinstall "Development Tools"
git clone https://github.com/robertdavidgraham/masscan
cd masscan
make
make install

```

不出意外这里make失败，报错信息



```
src/massip-addr.c: In function ‘ipv6address_selftest’:
src/massip-addr.c:292:7: warning: excess elements in union initializer [enabled by default]
       {NULL, {{0, 0}, 0}}
       ^
src/massip-addr.c:292:7: warning: (near initialization for ‘tests[8].ip_addr.’) [enabled by default]
src/massip-addr.c:299:3: error: ‘for’ loop initial declarations are only allowed in C99 mode
   for (int i = 0; tests[i].name != NULL; i++) {
   ^
src/massip-addr.c:299:3: note: use option -std=c99 or -std=gnu99 to compile your code
src/massip-addr.c:296:13: warning: unused variable ‘ip’ [-Wunused-variable]
   ipaddress ip;
             ^
make: *** [tmp/massip-addr.o] Error 1

```

根据提示`note: use option -std=c99 or -std=gnu99 to compile your code`，修改 Makefile 来加入`-std=c99`或`-std=gnu99`选项。在 Makefile 中找到 CFLAGS 定义部分，并添加以下内容：



```
CFLAGS += -std=gnu99

```

然后重新`make`，这次就成功了


![2024-12-27T09:08:58.png](https://img2024.cnblogs.com/blog/2747555/202412/2747555-20241228090133654-189066108.png)


完成之后，查看软件帮助信息：



```
masscan -h

```

## 获取活动主机


我这边ip段用的是纯真ip数据库，随便搜了一个地区



```
masscan x.x.0.0/16 -p80,8000-8008 -oL results --max-rate 8000

或者通过指定扫描文件

masscan -p80,8000-8008 -iL 目标文件 -oL 结果文件 --rate 8000

```

很快不用5分钟就扫描完了。扫描全网可以用下面的命令



```
masscan 0.0.0.0/0 -p0-65535 -oL all-results --max-rate 100000

```

masscan 运行完之后，将结果文件整理一下：`grep 'open' 结果文件 | awk '{printf"%s:%s\n", $4, $3}' > targets.txt`


结果文件整理成 `ip:port` 的格式


## 开始最终的扫描


之后对这些主机进行扫描：`python run_ingram.py -i targets.txt -o out`


![2024-12-27T09:20:58.png](https://img2024.cnblogs.com/blog/2747555/202412/2747555-20241228090133648-1939599103.png)


接下来就放在服务器慢慢跑吧




---


![image](https://img2024.cnblogs.com/blog/2747555/202412/2747555-20241218222717143-1042054688.png)


 本博客参考[PodHub豆荚加速器官方网站](https://rikeduke.com)。转载请注明出处！
