##  DHCP
lpicではISC DHCPを使用。  
###  dhcpd.conf  
設定ファイルは/etc/dhcpd.conf  
-  dhcp.leases  
どのipアドレスをどのクライアントに貸し出したかを記載するファイル。このファイルが無ければdhcpサーバを起動できない。  

####  設定項目  
-  defalut-lease-time  
クライアントへの貸し出し時間、クライアントからの指定がない場合  
指定しない場合43200秒（12時間）  
-  max-lease-time  
クライアントへの貸し出し時間、クライアントからの指定がある場合、この設定時間を超える要求があればこの値に設定される。  
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
####  dhcpv6  
ipアドレス（グローバルアドレス、ユニークローカル）、DNS、プレフィックス、一時アドレスを付与。  
一時アドレスは下位プレフィックスのインターフェイスid生成のmacアドレスではなく、仮のアドレスを生成する。  
####  デーモン  
-  dhcrelay  
DHCPリレーのデーモン。  
-  dhcpcd/dhclient  
DHCPクライアント  
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
認証前後に実施したい処理（ログの記録etc）を指定する。  

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
-  pam_cracklib.so→pam_pwquality.so  
パスワードの書式を制限する。  
    -  minlen パスワードに必要な最低文字数を設定。  
    -  difok  変更前とのパスワード文字数差分の数を設定  
-  pam_securetty.so  
rootユーザでのログインを/etc/securettyファイルに記載の端末（/dev配下のtty1などの端末名）以外拒否する。  
-  pam_nologin.so  
/etc/nologinがある場合、root以外のログインをできないようにする。
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
-  DN  
複数のRDNをカンマで区切って並べたもの。エントリ。  
-  RDN  
相対識別名。  
###  OpenLDAP  
LDAPサーバのフリーアプリケーション。  
LDAPサーバのデーモンはslapdで、  
/etc/openldap/slapd.confに設定ファイルを配置。  

####  slapd.conf  
openldapの設定ファイル。テキストベースの設定ファイル。  
slapdの起動時にslapd -fに続けてファイルを指定して起動も可能。  

####  slapd-config  
ディレクトリ形式で、LDIFファイルベースでの設定ファイルで構成。こちらの方が新しく、設定がある場合はslapd.confは無効化。  
-  suffix DN/olcSuffix  
ディレクトリのトップとなるDNを指定。  
-  rootdn DN/olcRootDN  データベース管理者のDNを指定。　　
-  argsfile/olcArgsFile  slapd起動時のコマンドの引数が入ったファイルを指定する。  
-  include/olcInclude  読み込む設定ファイルを指定  
-  logfile/olcLogFile  デバッグログ出力先を指定  
-  olcaccess  
アクセス制御のディレクティブ。  
-  olcbackend  
バックエンドを指定。  
####  openldapのコマンド  
-  slapcat  
slapdデータベースの内容を基にldifを生成する。  
    -  -l  LDIFファイルを指定の名前で生成する。（固有オプション）  
-  slappasswd  
サーバ管理者パスワードをハッシュ化して生成する。  
slapd.confに平文で記載する代わりにハッシュ値として保存することで流出リスクに備えることができる。  
    -  -s ハッシュ化するpwを指定。　-h ハッシュ方式を指定。-v 詳細情報を表示  
-  ldapsearch  
LDAPサーバのエントリを検索する。  
LDAPの認証を行ってから検索を行う。
    -  -x SASLを使用せずパスワードを平文


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

####  personなど属性名  
-  dc ドメインの構成要素  
-  cn commonname（一般名）  
-  sn surname（名字）  
-  o 組織名（会社や法人そのもの）  
-  ou 部署などの組織の単位  


####  ホワイトページ  
-  inetOrgPerson  
ホワイトページをLDAPで構築するために必要となるオブジェクトクラス。基底クラス→organizationalPerson→person  

###  sssd  
activedirectoryやldapなどの認証リモートサーバとクライアントを仲介する。  
クライアントプログラムに認証機能を与える。  
sssdがキャッシュ機能を持つことで、オフラインでも認証可能にする。  
####  sssd.conf  
sssdの設定ファイル。連携先のpamなどの情報を記載。  

##  語彙  
-  SASL  
認証とデータセキュリティをユーザが使用するためのフレームワーク。


