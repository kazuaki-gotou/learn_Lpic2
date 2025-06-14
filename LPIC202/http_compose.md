
<H3>firewall-cmd</H3>

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

```