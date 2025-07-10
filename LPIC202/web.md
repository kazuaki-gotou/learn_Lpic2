##  Apache  

ライセンスについて→https://www.apache.org/licenses/LICENSE-2.0

## httpd  

サーバプログラム（デーモン）  
起動すると設定ファイルにあたる、  
httpd.conf（rhel10のデフォルトは/etc/httpd/conf/httpd.confだった）を読み込み、  
記載されているディレクティブに従って動作する。  
各ディレクティブはどのコンテキストで使用できるかが定められている。  

###  httpd.conf  

apachectl configtest  
httpd.confの構文チェックをする。  

####  ディレクティブ  

設定ファイルの中でデーモンに動作の指示を与える。  

■セキュリティ  
- TraceEnable on/off  
traceメソッド（クライアントからのリクエストをそのまま返す）の有効無効を設定。  
BASIC認証のユーザ名パスワードを盗まれる可能性があるためOFF推奨。  
- ServerTokens   
クライアントに対しての返信の応答ヘッダに何を含めるのかを記載。  
apacheのバージョンを含めないことができる。  

■アクセス制御  
-  Order Allow Deny  
apache2.2でのアクセス制御ディレクティブ、現在は使用非推奨。デフォルトで全て拒否。（許可の記載がない限り、全拒否）  
-  Order Deny Allow  
デフォルトで全て許可。 （拒否の記載がない限り、全許可） 
-  require  
apache2.4でのアクセス制御ディレクティブ。  
  -  require all granted  
     全て許可　order deny allowと同じ。
  -  require all denied  
     全て拒否　order allow denyと同じ。  
  -  valid-user  
     パスワードファイル（.htpasswd）内にある全てのユーザが認証対象。  
-  Limit  
指定したhttpメソッド（DELETE PUSHなど）のアクセス制御が可能。  
-  Listen  
受け付けるipアドレスを設定する。IPベースのバーチャルホストにより2つIPアドレスがある場合でも対応可能。  
-  NameVirtualHost  
名前ベースバーチャルホストで受け付けるipアドレスを設定する。  
■外部設定ファイル  
・AccessFileName ファイル名  
外部設定ファイル名を指定。（デフォルトは.htaccess）  
・AllowOverride パラメータ  
外部設定ファイルにより、httpd.confの上書きを許可するかどうか。  
-  AuthConfig  
   認証関係の設定を許可。  
-  All すべての設定を許可。　None すべての設定を拒否。　　Limit  Order Allow Denyディレクティブを許可。  

■redirect/alias
-  redirect  
URLから指定のURLへのリダイレクトを指示する設定。
     -  [permanent|301] リソースが永久的に移動したことを示す。
     -  [temp|302] 一時的なリダイレクト（デフォルト）　　

-  alias  
新しいURLへリダイレクトするようにクライアントへ指示するredirectに対して、  
aliasはサーバ側で新しいページをリリースする。  

■モジュール  
・LoadModule  
動的モジュールをロードできるディレクティブ。　　
httpd.conf→Dynamic Shared Object (DSO) Support  
Ex）LoadModule foo_module modules/mod_foo.so  
・apxs  
動的モジュールをコンパイルとインストールできるコマンド。  
・[静的|動的]モジュール  
静的モジュールはapacheの起動時に必要なモジュールで、Apache本体に組み込まれる。  
動的モジュール（DSO）はhttpd.confのLoadModuleで有効無効の設定が可能。  

■プロセス処理  
・HostnameLookups[on|off]  
ログに出力されるIPアドレスを逆引きしてホスト名にする。  
接続数が増えると負荷が高くなるため、通常はoff。  
・LogLevel エラーログに出力するログレベルを指定する。  
・ErrorLog エラーログファイルを指定する。  
・User  ユーザ名orユーザID
httpd子プロセスの実行ユーザを指定  
・Group  グループ名orグループID
httpd子プロセスの実行グループを指定
・StartServers 子プロセス数  
httpdをスタートさせた時に立ち上げるhttpd子プロセス数を指定  
・MinSpareServers 子プロセス数  
待機httpd子プロセス数の最小数を指定。  
・MaxSpareServers 子プロセス数  
待機httpd子プロセス数の最大数を指定。  
・MaxRequestWorkers　子プロセス数  
生成されるhttpd子プロセスの最大数を指定。  
(同時応答リクエスト数を指定。)  

<h5>プロセス処理</h5>

-  Timeout（秒）  
クライアントからリクエストを受け取ってからコンテンツ提供終了までの処理上限時間。  
-  KeepAlive [on|off]  
1つのTCP接続で複数の接続を受け付けるかどうか。  
クライアントが1つのWebページを取得する間に、ファイルごとにTCP接続を開始/切断とするのは非効率。  
→KeepAliveを利用して1つの接続を使いまわすことで複数のリクエストに応じる。  
-  KeepAliveTimeout（秒）  
KeepAlive有効状態で、クライアントからのコンテンツ終了しても接続状態を切らない上限時間。  

■どうしてhttpdで子プロセスを使用するのか。  
複数の処理が来てしまった場合1つのプロセスで処理をすると限界が来る。　　
→子プロセスを複数立ち上げ待機させておくことで、それぞれがリクエストの処理を行わせることが可能になる。  
(1つの子プロセスにPHPの処理を、1つの子プロセスにHTMLの処理を)

MPM  
マルチプロセッシングモジュール  
apacheでクライアントのリクエストを並列処理する仕組み。  
以下はどのようにして子プロセスを捌くのか、リクエストの処理方法について記載されている。  
prework（apacheデフォルト）  
1つのリクエストに対して1つのプロセスがサービスを行う。  
worker  
1つのリクエストに対してプロセス内の1つのスレッドがサービスを行う。  

CGI  
common gateway interface  
WEBサーバがphpやPythonなどの外部プログラムから処理を受け取る、橋渡しをするIF。  

fastCGI  
CGIの改良版。  
外部アプリケーションをWEBサーバに接続するプログラムであったCGIだが、リクエストごとにプロセス生成していた。  
→一度動いているプログラムをしばらく待機させて、都度プロセス生成させる手間を省くことで、処理の高速化と負荷の軽減につなげる。  

<h4>バーチャルホスト</h4>  
一つのサーバで複数のWEBサイトを管理することができる。  
  
・名前ベースバーチャルホスト  
1台のpcに1つのipと複数のドメインを設定  

・IPベースバーチャルホスト  
1台のpcに複数のipと複数のドメインを設定  

・SNI(server name indication)  
SSL/TLSセッションハンドシェイク時に、クライアント側から接続したいホスト名を平文で伝達する。  
サーバからは適切な証明書を返すことができる。  
IPベースのバーチャルホストの場合は1つのIPにつき1つのドメインが割り当てられているが、名前ベースの場合は区別できない。  
また、SSL通信前ではどのホストとの通信かを判別するhttpヘッダを確認することができない。  
クライアント側もSNIに対応するブラウザでなければならない。（ほぼ対応している）  

<h4>認証</h4>

・BASIC認証とdigest認証  
apacheによるアクセス制限。アクセスのために予め登録しているユーザーネームとパスワードが必要。  
BASIC認証は認証情報のやりとりを平文で交わすので、盗聴されるリスクがあるが簡易的に利用できる。  
digest認証は認証情報のやりとりをハッシュ化している為、盗聴リスクがBASIC認証に比べて低い。  

####  コマンド
-  htpasswd  
     BASIC認証で用いるユーザ名とパスワードを設定。
-  htdigest ファイル名　認可領域名　ユーザ名  
     ダイジェスト認証のユーザ名とパスワードを設定。
-  .htpasswd  
     クライアントからブラウザからアクセス可能な領域に置いても参照出来なくするファイルネーム。  
     デフォルトで.htから始まる名前にすると有効。  

####  認証のモジュール  
-  authn_mod_core  
     認証（ユーザなど本人確認）を司るコアモジュール。  
-  authz_mod_core  
     認可（アクセス権など権限確認）を司るコアモジュール。  
-  コアモジュールとは？  
     認証や認可の枠組みそのもので、基盤であり土台。  
     その他のモジュールとの連携の中心。  
-  mod_authz_host  
    ホストベースのアクセス制御（apache2.4での動作互換→mod_access_compat　order allow denyの書式をそのまま利用できる。）

<h5>認証に関するディレクティブ</h5>

・AuthType  
認証方式を指定する。(basic digest)  

・Require  
認証対象とするユーザやグループを指定する。  

####  HTTPS

#####  ssl.conf

```
# find /etc | grep ssl.conf
/etc/httpd/conf.d/ssl.conf
/etc/httpd/conf.modules.d/00-ssl.conf
```

HTTPS通信をするためのモジュールであるmod_sslをインストールすると生成されるコンフィグファイル。　　
サーバ証明書やサーバ秘密鍵のパスについて設定する。　　

■opensslコマンド  
秘密鍵の作成や、CA証明書の送付などで使用する。  

■ディレクティブ  
-  SSLCertificateKeyFile  
サーバの秘密鍵ファイルを指定  
SSLCertificateKeyFile /etc/pki/tls/private/localhost.key  

-  SSLCertificateFile  
サーバ証明書のファイルを指定  
SSLCertificateFile /etc/pki/tls/certs/localhost.crt  

-  SSLCACertficateFile（単一指定）  
クライアント認証に使用するCA証明書のファイルを指定する。  
-  SSLCACertificatePath（複数指定）  
クライアント認証に使用するCA証明書のファイルが存在するディレクトリパスを指定する。  

####  nginx.conf  
-  コンテキスト  
ディレクティブの中で階層を持つことができるもの。  

#####  nginxディレクティブ  
-  index  
http://example.localdomain/のように/で終わる場合に返答するファイルを指定。  
-  listen（ip:portnum）  
ipアドレスポート番号を指定。  
-  location  
条件にマッチするURIがリクエストされた場合に実行する。  

###  squid  
linuxで最も使用されるプロキシサーバ。  
プロキシはクライアントからのアクセス代行をすることにより、アクセス制限を行うことができたり、
アクセスされたデータをキャッシュとして保存し、再アクセスではキャッシュを返すことでアクセススピードを上げる。  

squidは/etc/squid/squid.confが設定ファイル。 

####  squid.conf  
-  http_port （portnum）  
クライアントからのリクエストを受け付けるポートを指定。  
-  cache_mem  
メモリにキャッシュ「できる」最大サイズを指定する。  
-  maximum_object_size_in_memory  
メモリ上でキャッシュ「される」オブジェクトの最大サイズを指定する。　　
-  maximum(minimum)_object_size  
ディスクでキャッシュされるデータの最大（最小）サイズ。  
-  request_header_max_size  
httpリクエストヘッダの最大サイズを指定する。  
-  cache_dir  
キャッシュ用のディスク領域のディレクトリパスやサイズを指定する。  
[text](http_compose.md)  
-  hierarchy_stop_list  
キャッシュを利用しない文字列を指定。  
cgi-binや?などのキーワードは動的コンテンツのURLで頻発するワード。動的コンテンツでキャッシュを利用すると不具合が発生する可能性があるため、キャッシュ利用しない文字列に指定する。  

#####  squidのacl

squid.conf内に記載する。  
aclで制御したいアドレスレンジやポートなどのacl情報を指定してグループ化。 
書式　acl (aclグループ名)　src 192.168.0.0/255.255.255.0（src/dstなどのタイプ） 
http_accessで制御を設定。（allowdeny）  
書式　http_access allow (aclグループ名)  
-  srcdomain ドメイン名  
クライアントのドメイン名
-  dstdomain ドメイン名  
宛先ドメイン名  

