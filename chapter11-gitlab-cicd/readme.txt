一，https://about.gitlab.com/install/
1. new group 
2. new project
/etc/gitlab/gitlab.rb
sudo gitlab-ctl reconfigure

二，gitlab-ci 服务器搭建
1. 安装docker
2. 安装gitlab ci runner
3. 设置docker 权限
4. 注册runner

sudo gitlab-ci-multi-runner verify
三，docker machine 或vagrant 搭建dns
docker machine create
进入machine
docker run -d -p 53:53/tcp -p 53:53/udp --cap-add=NET_ADMIN --name dns-server andyshinn/dnsmasq
进入container
docker exec -it dns-server /bin/sh
-
vim /etc/dnsmasq/resolv.conf

nameserver 114.114.114.114
nameserver 8.8.8.8
-
vim /etc/dnsmasq/dnsmasqhosts

# 这里配置自定义解析
192.168.205.10 gitlab.fzh.com
192.168.205.12 registry.fzh.com
-
vi /etc/dnsmasq.conf

# 修改下述两个配置
resolv-file=/etc/dnsmasq/resolv.conf
addn-hosts=/etc/dnsmasq/dnsmasqhosts
-
回到宿主。重启dns-server容器
docker restart dns-server

测试
在gitlab-ci机器修改 sudo vim /etc/resolv.conf
nameserver 192.168.205.12 #docker machine ip

ping gitlab.fzh.com 
在gitlab-ci创建一个container，然后在里面ping 
docker run -it --rm busybox sh
ping gitlab.fzh.com

四， 注册docker 类型的runner

五， 搭建一个私有的docker registry
找一台docker host
docker run -d -v /opt/regisrty:/var/lib/registry -p 5000:5000 --restart=always --name registry registry
docker registry 绑定了本地的80端口，接下来配置DNS server ,假设运行registry 的机器的地址是192.168.205.12
找到DNS container
docker exec -it dns-server /bin/sh

添加一条新的记录
192.168.205.12 registry.fzh.com

重启container
docker restart dns-server

去gitlab-ci 服务器，ping
ping registry.fzh.com

测试
gitlab-ci 服务器上
sudo vim /etc/docker/daemon.json

{"insecure-registries":[" registry.fzh.com:5000"]}
sudo systemctl restart docker

docker pull busybox
docker tag busybox registry.fzh.com:5000/busybox

docker push registry.fzh.com:5000/busybox
