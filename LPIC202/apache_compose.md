
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

//
```
