#DHCP

###  dhcpd.conf  
設定ファイルは/etc/dhcpd.conf

####  設定項目  
-  defalut-lease-time  
クライアントへの貸し出し時間  
指定しない場合43200秒（12時間）  

-  option  
    -  option nis-servers IPアドレス;  
    nisサーバのipアドレスを指定。  

####  知識
-  NIS（Network Information Service） 
UNIX系システムで使用されるネットワーク管理サービスの一つ。（ディレクトリサービス）  
NISを利用すると、ネットワーク上の複数のコンピュータでユーザー情報やホスト名、  
グループ情報などを一元管理、クライアントに設定情報を共有する。  

###  LDAP  
https://blog.trustlogin.com/2022/20221027


