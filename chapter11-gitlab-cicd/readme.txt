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
配置真正的dns服务器，毕竟只是代理
vi /etc/resolv.dnsmasq
添加内容
nameserver 114.114.114.114 nameserver 8.8.8.8
192.168.211.10 gitlab.example.com #gitlab-server 的ip
修改dnsmasq 文件，指定使用上述自定义的配置文件
vi /etc/dnsmasq.conf
resolv-file=/etc/resolv.dnsmasq addn-hosts=/etc/dnsmasqhosts
回到宿主。重启dns-server容器
docker restart dns-server

测试
在gitlab-ci机器修改 sudo vim /etc/resolv.conf
nameserver 192.168.99.100 #docker machine ip

ping gitlab.example.com 
在gitlab-ci创建一个container，然后在里面ping 
docker run -it --rm busybox sh
ping gitlab.example.com

四， 注册docker 类型的runner