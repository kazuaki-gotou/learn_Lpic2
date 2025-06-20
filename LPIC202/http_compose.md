
```
# squid.conf – Sample Squid configuration
# Copyright (C) 2025 [kazuaki-gotou]
#
# This file is licensed under both the Apache License, Version 2.0, and
# the GNU General Public License, Version 3 (GPLv3).
# You may choose either of these licenses for use, modification, or distribution.
#
# SPDX-License-Identifier: Apache-2.0 OR GPL-3.0-or-later
```

###  firewall-cmd

```
# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: eth0
  sources:
  services: dhcpv6-client
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

//デフォルトがpublic
# firewall-cmd --get-default-zone
public

//--permanentで設定を恒久化。
//設定を入れないとfirewall-cmd --reloadで設定が消えてしまう。
# firewall-cmd --add-service http --permanent
success

# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: eth0
  sources:
  services: dhcpv6-client http
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
  
# firewall-cmd --reload
success

```

<h3>httpd.conf</h3>

```
//http://サーバのIPアドレス/privateへ接続可能

<Directory "/var/www/html/private">
    AllowOverride None
    # Allow open access:
    Require all granted
</Directory>

//http://サーバのIPアドレス/privateへ
//srcIP xx.xx.xx.xx/32のみが接続可能。
//他srcIPは接続不可（403エラー、ただしトップページへのアクセスは可能。）
<Directory "/var/www/html/private">
    AllowOverride None
    # Allow open access:
    Order Deny,Allow
    Deny from all
    Allow from xx.xx.xx.xx/32
</Directory>

```

<h3>nginx</h3>

```
//nginxのインストール
# tee /etc/yum.repos.d/nginx.repo <<EOF
> [nginx]
> name=nginx repo
> baseurl=http://nginx.org/packages/centos/7/\$basearch/
> gpgcheck=0
> enabled=1
> EOF
# yum install -y nginx

//nginx.conf
# pwd
/etc/nginx

# ls -ltr
合計 24
-rw-r--r-- 1 root root  648  5月 30  2024 nginx.conf
-rw-r--r-- 1 root root  664  5月 30  2024 uwsgi_params
-rw-r--r-- 1 root root  636  5月 30  2024 scgi_params
-rw-r--r-- 1 root root 5349  5月 30  2024 mime.types
-rw-r--r-- 1 root root 1007  5月 30  2024 fastcgi_params
lrwxrwxrwx 1 root root   29  6月 14 11:38 modules -> ../../usr/lib64/nginx/modules
drwxr-xr-x 2 root root   26  6月 14 11:38 conf.d

# vim nginx.conf
//バーチャルホストの記載（http内に配置）
http {
        server {
                listen 80;
                server_name www.exampletest.local;
                location / {
                        proxy_pass http://xx.xx.xx.xx/;
                        proxy_set_header Host $host;
                        proxy_pass_header Server;
                        }
        }
      }

//nginx.confのチェック
//nginx checks the configuration file syntax and then tries to open files referenced in the 
//configuration file.
# nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

<h3>squid</h3>

```
# pwd
/etc/squid
# ls -ltr
合計 48
-rw-r--r-- 1 root root   1808  4月 16  2024 errorpage.css
-rw-r--r-- 1 root root   1808  4月 16  2024 errorpage.css.default
-rw-r--r-- 1 root root   2244  4月 16  2024 squid.conf.default
-rw-r----- 1 root squid  2244  4月 16  2024 squid.conf
-rw-r--r-- 1 root root  12077  4月 16  2024 mime.conf.default
-rw-r--r-- 1 root root  12077  4月 16  2024 mime.conf
-rw-r--r-- 1 root root    692  4月 16  2024 cachemgr.conf.default
-rw-r--r-- 1 root squid   692  4月 16  2024 cachemgr.conf

# more squid.conf
//デフォルトのコンフィグ

#
# Recommended minimum configuration:
#

# Example rule allowing access from your local networks.
# Adapt to list your (internal) IP networks from where browsing
# should be allowed
acl localnet src 10.0.0.0/8     # RFC1918 possible internal network
acl localnet src 172.16.0.0/12  # RFC1918 possible internal network
acl localnet src 192.168.0.0/16 # RFC1918 possible internal network
acl localnet src fc00::/7       # RFC 4193 local private network range
acl localnet src fe80::/10      # RFC 4291 link-local (directly plugged) machines

acl SSL_ports port 443
acl Safe_ports port 80          # http
acl Safe_ports port 21          # ftp
acl Safe_ports port 443         # https
acl Safe_ports port 210         # wais
acl Safe_ports port 1025-65535  # unregistered ports
acl Safe_ports port 280         # http-mgmt
acl Safe_ports port 488         # gss-http
acl Safe_ports port 591         # filemaker
acl Safe_ports port 777         # multiling http
acl CONNECT method CONNECT

#
# Recommended minimum Access Permission configuration:
#
# Deny requests to certain unsafe ports
http_access deny !Safe_ports

# Deny CONNECT to other than secure SSL ports
http_access deny CONNECT !SSL_ports

# Only allow cachemgr access from localhost
http_access allow localhost manager
http_access deny manager

# We strongly recommend the following be uncommented to protect innocent
# web applications running on the proxy server who think the only
# one who can access services on "localhost" is a local user
#http_access deny to_localhost

#
# INSERT YOUR OWN RULE(S) HERE TO ALLOW ACCESS FROM YOUR CLIENTS
#

# Example rule allowing access from your local networks.
# Adapt localnet in the ACL section to list your (internal) IP networks
# from where browsing should be allowed
http_access allow localnet
http_access allow localhost

# And finally deny all other access to this proxy
http_access deny all

# Squid normally listens to port 3128
http_port 3128

# Uncomment and adjust the following to add a disk cache directory.
#cache_dir ufs /var/spool/squid 100 16 256
→→# pwd
/var/spool/squid
# ls -F
00/  01/  02/  03/  04/  05/  06/  07/  08/  09/  0A/  0B/  0C/  0D/  0E/  0F/  swap.state

# Leave coredumps in the first cache dir
coredump_dir /var/spool/squid

#
# Add any of your own refresh_pattern entries above these.
#
refresh_pattern ^ftp:           1440    20%     10080
refresh_pattern -i (/cgi-bin/|\?) 0     0%      0
refresh_pattern .               0       20%     4320
```
