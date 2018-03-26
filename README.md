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
getenforce <br />
sestatus -v <br /><br />
setenforce 0 <br />
vim /etc/selinux/config <br /> 
SELINUX=disabled <br />

>1.3 ntp 时间同步
ntpdate -u cn.pool.ntp.org <br />

>1.4 安装docker <br /> 
systemctl enable docker <br /> 
systemctl start docker <br />
vim /lib/systemd/system/docker.service <br /> 
ExecStartPost=/sbin/iptables -I FORWARD -s 0.0.0.0/0 -j ACCEPT <br /><br />
mkdir -p /etc/docker<br />
tee /etc/docker/daemon.json <<-'EOF'<br />
{<br />
  "registry-mirrors": ["mirror 镜像地址"]<br />
}<br />
EOF<br /><br />
systemctl daemon-reload <br /> 
systemctl restart docker <br /> 

>1.5 配置内核参数 <br />
cat &lt;&lt;EOF > /etc/sysctl.d/usernetwork.conf<br />
net.ipv4.ip_forward = 1<br />
net.bridge.bridge-nf-call-ip6tables = 1<br />
net.bridge.bridge-nf-call-iptables = 1<br />
EOF<br /><br />
sysctl -p /etc/sysctl.d/usernetwork.conf

### 2. 部署etcd
>2.1下载合适的发行包, 将etcd和etcdctl复制到/usr/bin

>2.2应用配置 <br />
mkdir /etc/etcd/ <br />
cat &lt;&lt;EOF > /etc/etcd/etcd.conf <br />
ETCD_NAME=default <br />
ETCD_DATA_DIR="/var/lib/etcd/default.etcd" <br />
ETCD_LISTEN_CLIENT_URLS="http://127.0.0.1:2379,http://192.168.100.100:2379" <br />
ETCD_ADVERTISE_CLIENT_URLS="http://192.168.100.100:2379" <br />
EOF

>2.3服务配置 <br />
mkdir /var/lib/etcd <br />
cat &lt;&lt;EOF > /usr/lib/systemd/system/etcd.service <br />
[Unit] <br />
Description=Etcd Server <br />
After=network.target <br /> <br />
[Service] <br />
Type=simple <br />
WorkingDirectory=/var/lib/etcd/ <br />
EnvironmentFile=-/etc/etcd/etcd.conf <br />
ExecStart=/usr/bin/etcd <br /> <br />
[Install] <br />
WantedBy=multi-user.target <br />
EOF <br />

>2.4启动服务 <br />
systemctl enable etcd <br />
systemctl start etcd <br /> <br />
ETCDCTL_API=3 <br />
etcdctl put foo bar

### 3. 部署fannal网络方案

### 4. 部署master节点

### 5. 部署node节点

### 附录
A.切换yum镜像源 <br />
    备份: mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup <br />
    更新: wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo <br />

B.设置时区 <br />
    tzselect <br />
