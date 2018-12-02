ssrpanel后端安装(google云)

关闭防火墙
	centos6
	service iptables stop    #关闭命令  
	chkconfig iptables off   # 永久关闭防火墙
	centos7
	systemctl stop firewalld.service	#关闭防火墙
	systemctl disable firewalld.service #关闭开机启动

谷歌加速安装[OpenVZ 以外的（ KVM 、 Xen 、 VMware 等）]
	cd /root
	yum install wget -y
	wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh
	chmod +x bbr.sh
	./bbr.sh

查看BBR是否启动
	lsmod | grep bbr


环境安装	
	yum -y groupinstall "Development Tools"
	wget https://github.com/jedisct1/libsodium/releases/download/1.0.15/libsodium-1.0.15.tar.gz
	tar xf libsodium-1.0.15.tar.gz && cd libsodium-1.0.15
	./configure && make -j2 && make install
	echo /usr/local/lib > /etc/ld.so.conf.d/usr_local_lib.conf
	ldconfig

后端程序
	cd /root
	git clone -b manyuser https://github.com/shadowsocksrr/shadowsocksr.git
	git clone -b manyuser https://github.com/glzjin/shadowsocks.git
	cd shadowsocksr
	cp setup_cymysql.sh ../shadowsocks
	cp initcfg.sh ../shadowsocks
	cd ../shadowsocks
	./setup_cymysql.sh
	./initcfg.sh

设置程序的配置文件
	cp apiconfig.py userapiconfig.py
	cp config.json user-config.json
	vi userapiconfig.py
		配置文件（需要修改的地方）
		# Config
		NODE_ID = 8
		# hour,set 0 to disable
		SPEEDTEST = 1
		SERVER_PUB_ADDR = '45.32.000.00'  # mujson_mgr need this to generate ssr link
		API_INTERFACE = 'glzjinmod'  # glzjinmod, modwebapi
		WEBAPI_URL = 'http://45.32.000.00/mu'
		WEBAPI_TOKEN = '****'
		# Mysql
		MYSQL_HOST = '45.32.000.00'
		MYSQL_PORT = 3306
		MYSQL_USER = 'root'
		MYSQL_PASS = '***'
		MYSQL_DB = 'root'
		# API
		API_HOST = '45.32.000.00'
		API_PORT = 80
		API_PATH = '/mu/v3/'

修改文件
	vi userapiconfig.py
		# Config
		API_INTERFACE = 'glzjinmod' 
	vi user-config.json
		"connect_verbose_info": 1,
	vi usermysql.json
		"host": "45.76.000.00",
		"port": 3306,
		"user": "root",
		"password": "****",
		"db": "root",
		"node_id": 14,
		
安装supervisor守护进程
	
一、安装supervisor
       
   yum install python-setuptools
     
   easy_install supervisor


二、修改supervisor 配置文件
      
  supervisord
      
  mkdir /etc/supervisor
    
  echo_supervisord_conf > /etc/supervisor/supervisord.conf
      
   vi /etc/supervisor/supervisord.conf

[program:ssr]

command=python /root/shadowsocks/shadowsocks/server.py

autorestart=true

autostart=true

user=root


三、启动supervisor服务


       supervisord

       supervisorctl

        ->reload

        ->restart ssr


打开 supervisor 命令行及常用命令


      supervisorctl

          ->status         # 查看状态

          ->stop ssr      # 停止 shadowsocks

          ->start ssr      # 打开

          ->restart ssr   # 重启
测速
	speedtest-cli

关于升级
	cd shadowsocks
	git pull

安装requirements.txt报错处理
	pip freeze > requirements.txt
	pip install -r requirements.txt

无法安装libsodium
	yum install gcc-c++


