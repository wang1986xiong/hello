linux 下命令行代理建议配置 proxychains socks5 来使用

### 1 酸酸乳 linux mac 配置
拉代码
```
git clone https://github.com/shadowsocksr-backup/shadowsocksr.git
```
配置文件
```
{
    "server": "svip21.mm1080p.rocks",
    "server_ipv6": "::",
    "server_port": 443,
    "local_address": "127.0.0.1",
    "local_port": 1088,

    "password": "123456",
    "method": "aes-256-cfb",
    "protocol": "auth_aes128_md5",
    "protocol_param": "端口:密码",
    "obfs": "tls1.2_ticket_auth",
    "obfs_param": "",
    "speed_limit_per_con": 0,
    "speed_limit_per_user": 0,

    "additional_ports" : {},
    "additional_ports_only" : false, 
    "timeout": 120,
    "udp_timeout": 60,
    "dns_ipv6": false,
    "connect_verbose_info": 0,
    "redirect": "",
    "fast_open": false
}
```
说明：
443 端口这种配置方法适用于端口大于 9680 的新同学，如果你是之前老同学。直接修改 server_port 和 password 即可，protocol_param 空白。

配置并且启动 ssr 客户端
```
> vim ssr_local.json    然后填入上面的配置文件内容
> /root/shadowsocksr/shadowsocks/local.py -c /root/ssr_local.json -d start
```
### 2 v 2ray 命令行配置
获取客户端 
```
https://github.com/v2ray/v2ray-core/releases
```
配置 conf.json 文件如下
```
{
  "log": {
    "loglevel": "warning"
  },
 
  "inbound": {
    "port": 1077,
    "listen": "127.0.0.1",
    "protocol": "socks",
    "settings": {
      "auth": "noauth",
      "udp": false,
      "ip": "127.0.0.1"
    }
  },
 
  "outbound": {
    "protocol": "vmess",
 
    "settings": {
      "vnext": [{
        "address": "svip21.mm1080p.rocks",
        "port": 10086,
        "users": [{
          "id": "这里换成你的 uid",
          "alterId": 10
        }]
      }]
    },
 
    "streamSettings": {
      "network": "tcp",
      "security": "tls"
    }
  },
 
  "policy": {
    "levels": {
      "0": {
        "uplinkOnly": 0
      }
    }
  }
}
```

配置好后，本地就可以通过 socks5 协议连接了 127.0.0.1 端口 1077

### 3 proxychains ubuntu 下命令行代理配置
#### 3.1 获取 v2ray
```
wget https://github.com/v2ray/v2ray-core/releases/download/v3.22/v2ray-linux-64.zip
unzip v2ray-linux-64.zip
cd v2ray-v3.22-linux-64/
```
#### 3.2 配置文件
```
vim local_conf.json
填上面的配置文件内容

chmod +x local_conf.json
nohup ./v2ray -config local_conf.json &
```
这样 v2ray 就跑起来了 本地 socks5 端口为 1077

#### 3.3 配置 proxychains
```
apt-get install -y proxychains
vim /etc/proxychains.conf
```
修改成如下
```
[ProxyList]
# add proxy here ...
# meanwile
# defaults set to "tor"
socks5  127.0.0.1 1077
```
这样就成功了 测试一下呢 curl 和 git clone 的代理速度
```
proxychains curl https://www.google.com/?gws_rd=ssl
proxychains git clone https://github.com/torvalds/linux.git
```
Have fun!
