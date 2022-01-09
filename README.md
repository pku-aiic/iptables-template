# iptables-template


## iptables 设置

```shell

# 清空状态
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT

iptables -F #清空所有默认规则
iptables -X #清空所有自定义规则
iptables -Z #所有计数器归0

# ssh 进来
iptables -A INPUT -p tcp --dport 60022 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 60022 -j ACCEPT

# ssh 出去
iptables -A OUTPUT -p tcp --dport 60022 -j ACCEPT
iptables -A INPUT -p tcp --sport 60022 -j ACCEPT

# ping
iptables -A INPUT -p icmp --icmp-type 8 -j ACCEPT    # 允许ping
iptables -A OUTPUT -p icmp --icmp-type 8 -j ACCEPT    # 允许ping

# dns
iptables -A OUTPUT -p udp --dport 53 -j ACCEPT # dns
iptables -A INPUT -p udp --sport 53 -j ACCEPT # dns
iptables -A INPUT -p udp --dport 53 -j ACCEPT # dns
iptables -A OUTPUT -p udp --sport 53 -j ACCEPT # dns

# Allow incoming web traffic
iptables -A INPUT -p tcp --sport 80 -j ACCEPT
iptables -A OUTPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --sport 443 -j ACCEPT
iptables -A OUTPUT -p tcp --dport 443 -j ACCEPT

iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 443 -j ACCEPT

# 
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# 允许localhost的所有访问，
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# 访问外部的gitlab的40149端口
iptables -A OUTPUT -p tcp --dport 40149 -j ACCEPT

# 特定端口 开发服务, e.g. 6868
iptables -A INPUT -p tcp  --dport 6868 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 6868 -j ACCEPT
iptables -A OUTPUT -p tcp --dport 6868 -j ACCEPT
iptables -A INPUT -p tcp  --sport 6868 -j ACCEPT

iptables -A INPUT -p tcp  --dport 8000 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 8000 -j ACCEPT
iptables -A OUTPUT -p tcp --dport 8000 -j ACCEPT
iptables -A INPUT -p tcp  --sport 8000 -j ACCEPT

iptables -A INPUT -p tcp  --dport 50051 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 50051 -j ACCEPT
iptables -A OUTPUT -p tcp --dport 50051 -j ACCEPT
iptables -A INPUT -p tcp  --sport 50051 -j ACCEPT

# 特定端口 range
iptables -A INPUT  -p tcp --dport 6868:6900 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 6868:6900 -j ACCEPT
iptables -A OUTPUT -p tcp --dport 6868:6900 -j ACCEPT
iptables -A INPUT  -p tcp --sport 6868:6900 -j ACCEPT

# 特定端口 multiport
iptables -A INPUT -p tcp -m multiport --dports 7676,7373,9200,7070,8086,9201,6161 -j ACCEPT
iptables -A INPUT -p tcp -m multiport --sports 7676,7373,9200,7070,8086,9201,6161 -j ACCEPT
iptables -A OUTPUT -p tcp -m multiport --dports 7676,7373,9200,7070,8086,9201,6161 -j ACCEPT
iptables -A OUTPUT -p tcp -m multiport --sports 7676,7373,9200,7070,8086,9201,6161 -j ACCEPT

# snmp
iptables -A INPUT -p udp -m udp --dport 161 -j ACCEPT
iptables -A INPUT -p udp -m udp --dport 162 -j ACCEPT

# DROP ALL
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT DROP

```

## 永久保存

```shell
###########################
# ubuntu 永久保存
# https://www.thomas-krenn.com/en/wiki/Saving_Iptables_Firewall_Rules_Permanently
apt-get install iptables-persistent
# /etc/iptables/rules.v4 for IPv4 and /etc/iptables/rules.v6 for IPv6
iptables-save > /etc/iptables/rules.v4

# centos7 永久保存
# https://www.thegeekdiary.com/centos-rhel-7-unable-to-startenable-iptables/
yum install iptables-services
service iptables save
# 或者：iptables-save > /etc/sysconfig/iptables
chkconfig iptables on # 开机启动

```

## Check

```
# check
host www.baidu.com
ping www.baidu.com
wget www.baidu.com
```


参考
* https://forum.linuxfoundation.org/discussion/7024/iptables-is-blocking-wget-solved
* https://windard.com/opinion/2016/10/15/About-Iptables
