### rsync 和scp
scp:单纯的服务器与服务器之间的全部copy
rsync:数据同步，只会拷贝两者之间不相同的部分，用来更新代码之类速度很快
scp： scp root@xxx.xxx.x.xx://root/*  ./                      拷贝目标服务器root下的所有到本地
sync: rsync -avz root@xxx.xxx.xxx.xx::[]  ./                  同步目标服务器配置文件中path下的所有数据到本地 

### master

mkdir  -p  /home/lee/rsync 创建同步目录

### 配置文件
motd file = /etc/rsyncd.motd
transfer logging = yes
log file = /var/log/rsyncd.log
port = 873
address = 192.168.8.81
uid = root
gid = root
use chroot = no
read only = no
max connections = 10
[common]
comment = rsync lee
path = /home/lee/rsync/        #拷贝的文件所在
ignore errors
auth users = lee
secrets file = /etc/rsyncd.secrets
hosts allow = 192.168.8.0/255.255.255.0
hosts deny = *
list = false

### 流程
1. 创建用户密码：echo  "lee:123456"  >  /etc/rsyncd.secrets
chmod  600  /etc/rsyncd.secrets
2. 创建提示信息文件：echo  "rsync  lee"  >  /etc/rsyncd.motd
3. 配置防火墙规则：iptables  -I  INPUT  -p  tcp  --dport  873  -j  ACCEPT
4. 启动服务：rsync  --daemon
5. 开机自启，设置rsync.service



### client
1. yum  -y  install  rsync
2. 创建需同步的目录：mkdir  -p  /home/lee/rsync
3. 创建密码文件（免输入密码）：echo  "123456"  >  /root/passwd
chmod  600  /root/passwd
4. 拉取：rsync  -avz  --password-file=/root/passwd  lee@192.168.8.81::common  /home/lee/rsync/
   推送：rsync  -avz  --password-file=/root/passwd  /home/lee/rsync/  lee@192.168.8.81::common



