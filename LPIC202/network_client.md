##  DHCP

###  dhcpd.conf  
設定ファイルは/etc/dhcpd.conf

####  設定項目  
-  defalut-lease-time  
クライアントへの貸し出し時間  
指定しない場合43200秒（12時間）  

-  option  
    -  option nis-servers IPアドレス;  
    nisサーバのipアドレスを指定。  

####  ログ  
cat /var/log/messages | grep dhcp  
journalctl | grep dhcp  
systemctl status dhcpd （全てのログではなく最新の一部のみ）  
###  dhcrelay  
DHCPリレーのデーモン。  

####  知識
-  NIS（Network Information Service） 
UNIX系システムで使用されるネットワーク管理サービスの一つ。（ディレクトリサービス）  
NISを利用すると、ネットワーク上の複数のコンピュータでユーザー情報やホスト名、  
グループ情報などを一元管理、クライアントに設定情報を共有する。  

##  PAM  
認証機能を取りまとめて、プログラムにユーザ認証機能を提供する。  

###  /etc/pam.d  
PAMの設定についてを取りまとめたディレクトリ。

####  モジュールタイプ  
モジュールに何をさせるか、どのタイプの認証を行うのかを決める。

####  コントローラ  
モジュールの実行に成功あるいは失敗した際の処理方法について記載。  
-  requisite  
モジュールの実行に失敗した場合、それ以降のモジュールは実施せず認証を拒否。  
-  required  
モジュールの実行に失敗した場合、同じタイプ（auth、accountなどのmanagement group/モジュールタイプ）のモジュールを実施してから認証を拒否する。  
-  sufficient  
モジュールの実行に成功した場合、この行以前のrequiredモジュールですべて成功していれば、認証を許可する。  
失敗していれば次の行のモジュールが実行する。  

####  モジュール  
使用するモジュールを決める。  
モジュールファイルは、/lib/security（/lib64/security）に格納されており、フルパス指定しなければこのディレクトリを指定する。  

-  pam_unix.so  
通常のパスワード認証。  
-  pam_cracklib.so  
パスワードの書式を制限する。  
-  pam_securetty.so  
rootユーザでのログインを/etc/securettyファイルに記載の端末（/dev配下のtty1などの端末名）以外拒否する。  
-  pam_deny.so  
認証をすべて拒否する。  
-  pam_warn.so  
認証やパスワード変更などのイベントが起きた場合、ログに記録する。  
-  pam_limits.so  
ユーザが使用するリソースを制限する。  


##  LDAP  
https://blog.trustlogin.com/2022/20221027  

###  OpenLDAP  
LDAPサーバのフリーアプリケーション。  
LDAPサーバのデーモンはslapdで、  
/etc/openldap/slapd.confに設定ファイルを配置。  

####  slapd.conf  
openldapの設定ファイル。テキストベースの設定ファイル。  

####  slapd-config  
ディレクトリ形式で、LDIFファイルベースでの設定ファイルで構成。  
-  olcaccess  
アクセス制御のディレクティブ。  

####  openldapのコマンド  
-  slapcat  
slapdデータベースの内容を基にldifを生成する。  
-  slappasswd  
サーバ管理者パスワードをハッシュ化して生成する。  
slapd.confに平文で記載する代わりにハッシュ値として保存することで流出リスクに備えることができる。  
    -  -s ハッシュ化するpwを指定。　-h ハッシュ方式を指定。  

####  オブジェクトクラス  
#####  person属性  
-  cn commonname（一般名）  
-  sn surname（名字）  

#####  ホワイトページ  
-  inetOrgPerson  
ホワイトページをLDAPで構築するために必要となるオブジェクトクラス。  

###  sssd  
activedirectoryやldapなどの認証リモートサーバとクライアントを仲介する。  
クライアントプログラムに認証機能を与える。  
sssdがキャッシュ機能を持つことで、オフラインでも認証可能にする。  




