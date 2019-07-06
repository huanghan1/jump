# jump
翻墙软件安装

1.电脑翻墙部署 

    国际服务器
    
    1.yum -y  install python-pip 
    
	2.pip install shadowsocks
	 
	3.ssserver -k  AAAaaa111 -p 17131 -d start --pid-file /tmp/17131
	
	4.sslocal -s 127.0.0.1 -p 17131 -k  AAAaaa111 -d start --pid-file /tmp/sslocal.pid --log-file /tmp/sslocal.log

	国内服务器代理（152.32.164.52 国际ip）
  
	1.yum -y  install python-pip 
  
	2.pip install shadowsocks   （socks5协议）
  
	   sslocal -s 152.32.164.52 -p 17131 -b 0.0.0.0 -l 1081 -k AAAaaa111 --log-file /tmp/1081.log

	3.yum -y install git texinfo （http 协议 172.16.11.34内网IP）
  
	4.git clone https://github.com/jech/polipo.git
  
	5.cd polipo
  
	6.make -sj 
  
	7.make install
  
	cat polipo.conf 
  
		logSyslog = true
		logFile = /tmp/polipo1.log
		proxyAddress = "0.0.0.0"
		socksParentProxy = "127.0.0.1:1081"
		socksProxyType = socks5
		proxyPort = 8123
		chunkHighMark = 50331648
		objectHighMark = 16384
		serverMaxSlots = 128
		serverSlots = 16
		serverSlots1 = 32
    
	nohup root/polipo/polipo -c polipo.conf &
	
	8.yum -y install haproy
	  
	cat haproxy.cfg
	global
    log         127.0.0.1 local2

    chroot      /var/lib/haproxy
    pidfile     /var/run/haproxy.pid
    maxconn     4000
    user        haproxy
    group       haproxy
    daemon

 
    stats socket /var/lib/haproxy/stats
	defaults
    mode tcp
    timeout connect 5000
    timeout client  50000
    timeout server  50000

	frontend shadowsocks-in
    mode tcp
    bind *:1088
    default_backend shadowsocks-out

	backend shadowsocks-out
    mode tcp
    #balance roundrobin
    balance  source
        server  server1_name   172.16.11.34:1081

	frontend http-proxy
    mode tcp
    bind *:8123
    default_backend http-proxy-server

	backend http-proxy-server
    mode tcp
    #balance roundrobin
    balance  source
        server  server1_name   172.16.11.34:8123

163.
