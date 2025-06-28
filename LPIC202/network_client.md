##  DHCP

###  dhcpd.conf  
設定ファイルは/etc/dhcpd.conf

####  設定項目  
-  defalut-lease-time  
クライアントへの貸し出し時間  
指定しない場合43200秒（12時間）  
-  fixed-address IPアドレス;  
クライアントに割り当てる固定ipアドレスを設定  
macアドレスを指定して割り当て、予約席みたいなもの  
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

####  スタック(モジュールタイプ)  
モジュールに何をさせるか、どのタイプの認証を行うのかを決める。  
-  auth  
パスワードを使用してユーザの認証を行う。  
-  account  
ユーザのアカウントが有効かどうかをチェックする。  
-  password  
パスワードの設定および変更を行う。  
-  session  
認証前後に実施したい処理を指定する。  

####  コントローラ  
モジュールの実行に成功あるいは失敗した際の処理方法について記載。  
-  requisite  
モジュールの実行に失敗した場合、それ以降のモジュールは実施せず認証を拒否。  
-  required  
モジュールの実行に失敗した場合、同じタイプ（auth、accountなどのmanagement group/モジュールタイプ）のモジュールを実施してから認証を拒否する。  
-  sufficient  
モジュールの実行に成功した場合、この行以前のrequiredモジュールですべて成功していれば、認証を許可する。  
失敗していれば次の行のモジュールが実行する。  
-  optional  
モジュールの実行の成功失敗如何に問わず、次の行のモジュールを実行する。　　
認証結果に関係ないタスクを実行するために使用する。　　

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
-  pam_env.so  
環境変数の設定。  

##  LDAP  
https://blog.trustlogin.com/2022/20221027  

###  OpenLDAP  
LDAPサーバのフリーアプリケーション。  
LDAPサーバのデーモンはslapdで、  
/etc/openldap/slapd.confに設定ファイルを配置。  

####  slapd.conf  
openldapの設定ファイル。テキストベースの設定ファイル。  

####  slapd-config  
ディレクトリ形式で、LDIFファイルベースでの設定ファイルで構成。こちらの方が新しく、設定がある場合はslapd.confは無効化。  
-  suffix DN/olcSuffix  
ディレクトリのトップとなるDNを指定。  
-  rootdn DN/olcRootDN  データベース管理者のDNを指定。　　
-  argsfile/olcArgsFile  slapd起動時のコマンドの引数が入ったファイルを指定する。  
-  include/olcInclude  読み込む設定ファイルを指定  
-  olcaccess  
アクセス制御のディレクティブ。  
####  openldapのコマンド  
-  slapcat  
slapdデータベースの内容を基にldifを生成する。  
-  slappasswd  
サーバ管理者パスワードをハッシュ化して生成する。  
slapd.confに平文で記載する代わりにハッシュ値として保存することで流出リスクに備えることができる。  
    -  -s ハッシュ化するpwを指定。　-h ハッシュ方式を指定。  
-  ldapsearch  
LDAPサーバのエントリを検索する。


####  オブジェクトクラス  
-  ABSTRACT  
-  STRUCTURAL
-  AUXILIARY

####  スキーマ LDIF  
オブジェクトクラスや属性はスキーマファイルで定義されている。　　
スキーマについてはslapd.confのincludeで読み込む。  
slapd-configの場合はLDIFで動的読み込み。
-  core.schema  core.ldif  
cn ou などの基本的な属性を定義する。必須。　　

####  person属性  
-  cn commonname（一般名）  
-  sn surname（名字）  

####  ホワイトページ  
-  inetOrgPerson  
ホワイトページをLDAPで構築するために必要となるオブジェクトクラス。  

###  sssd  
activedirectoryやldapなどの認証リモートサーバとクライアントを仲介する。  
クライアントプログラムに認証機能を与える。  
sssdがキャッシュ機能を持つことで、オフラインでも認証可能にする。  

##  語彙  
-  SASL  


