# kubernetes 安装指南

### 1. 准备主机集群，并初始化各主机环境
使用vagrant创建一个3节点的主机集群，主机信息如下: <br />

> master：192.168.100.100<br />
> node1 ：192.168.100.101<br />
> node2 ：192.168.100.102<br />

具体脚本请见 [[Vagrantfile]](https://github.com/endless-space/k8s-install/blob/master/Vagrantfile) 

>1.1 关闭防火墙 <br /> 
systemctl disable firewalld.service <br /> 
systemctl stop firewalld.service <br /> 

>1.2 关闭selinux <br /> 
systemctl stop firewalld<br /> 
systemctl disable firewalld <br /><br />
setenforce 0 <br />
vim /etc/selinux/config <br /> 
SELINUX=disabled <br /> 

>1.3 安装docker <br /> 
systemctl enable docker <br /> 
systemctl start docker <br />
vim /lib/systemd/system/docker.service <br /> 
ExecStartPost=/sbin/iptables -I FORWARD -s 0.0.0.0/0 -j ACCEPT <br /> 
systemctl daemon-reload <br /> 
systemctl restart docker <br /> 

>1.4 ntp 时间同步
ntpdate -u cn.pool.ntp.org <br />

>1.5 配置内核参数 <br />
cat <<EOF > /etc/sysctl.d/k8s.conf<br />
net.ipv4.ip_forward = 1<br />
net.bridge.bridge-nf-call-ip6tables = 1<br />
net.bridge.bridge-nf-call-iptables = 1<br />
EOF<br /><br />
sysctl -p /etc/sysctl.d/k8s.conf


### 2. 部署etcd

### 3. 部署fannal网络方案

### 4. 部署master节点

### 5. 部署node节点

### 附录
