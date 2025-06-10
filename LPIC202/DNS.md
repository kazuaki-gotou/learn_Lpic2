<h1>DNS</h1>
  
bind  
linuxのdnsソフトウェア  

named  
bindにおいて、dnsのサービスを提供するデーモン。  

<h3>リソースレコード</h3>

・PTR  
逆引き 正引きとは反対にIPアドレスからドメイン名を調べること  
逆引きを行うにはPTRレコードが必要で、特定のIPアドレスに対応するホスト名を定義する。  
$ORIGIN 3.168.192.in-addr.arpa.  
ログ解析やネットワークトラブルシューティング、アクセス制限などに役立ち、ドメイン名を知ることでアクセス元の特定や認証に利用できる。  
・SOA  
ゾーンに関する情報を記載。  
管理者のメールアドレスや、更新に関する時間を定義。  
・NS  
ゾーンの情報を管理するサーバ名を記載。  
・MX  
ゾーンで管理するドメイン宛のメールの宛先メールサーバを記述。  


<h3>named.conf</h3>  

BIND（named）の設定ファイル。/etc/named.conf  
namedの動作に関する詳細な設定を行う。

<h4>optionsステートメント</h4>  

```
options {
        listen-on port 53 { 127.0.0.1; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        recursing-file  "/var/named/data/named.recursing";
        secroots-file   "/var/named/data/named.secroots";
        allow-query     { localhost; };

        /*
         - If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
         - If you are building a RECURSIVE (caching) DNS server, you need to enable
           recursion.
         - If your recursive DNS server has a public IP address, you MUST enable access
           control to limit queries to your legitimate users. Failing to do so will
           cause your server to become part of large scale DNS amplification
           attacks. Implementing BCP38 within your network would greatly
           reduce such attack surface
        */
        recursion yes;

        dnssec-enable yes;
        dnssec-validation yes;

        /* Path to ISC DLV key */
        bindkeys-file "/etc/named.root.key";

        managed-keys-directory "/var/named/dynamic";

        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";
};
```

・directory  
namedの作業ディレクトリ  /var/named など  
zoneステートメントでディレクトリを指定しなければ、  
optionsステートメントで指定したディレクトリにZoneファイルが設置される  
→zone ステートメントでファイルパスを指定するときに、　　
絶対パスを使用した場合は指定したその場所になり、　　
相対パスを使用した場合は、options ステートメントで指定した directory のディレクトリを基準にした場所になる。　　

・recursion  
再帰的問い合わせを受け付けるかの設定をする【YES|NO】
yesの場合はキャッシュサーバとなる。  

・forwarders  
自身が保持しないドメイン情報の問い合わせを転送するDNSサーバのアドレスを指定。

・forward
問い合わせ転送の失敗時の動作設定【first|only】  
first・・・フォワードによる名前解決を試みて、  
失敗した場合は自身で再帰問い合わせ
only・・・失敗した場合は自身で再帰問い合わせしない。  

・allow-query  
問い合わせを受け付けるホストを指定する。  

・allow-transfer
ゾーン転送を許可するホストを指定。  
マスターDNSサーバからスレーブDNSのアドレスを指定する。  
マスターサーバ自身が持つゾーンファイルの内容を、スレーブに転送することでゾーン情報を共有する。
→その際のスレーブDNSサーバのアドレスを指定する際に使用する。  

<h4>includeステートメント</h4> 
設定ファイルを読み込み、/etc/name.confの設定に追加する。   

<h4>controlsステートメント</h4>   
rndcコマンドの使用を許可するホストを指定。  

/usr/sbin/named  
起動すると最初に/etc/named.confを読み込む    

named-checkconf  
/etc/name.confの書式誤りをチェックする。  
オプション-zでゾーンファイルの内容もチェックする。  

```
# named-checkconf
/etc/named.conf:20: unknown option '??'
```

<h5>ログ出力</h5>
/etc/named.confでログ出力先を指定していない場合は、デフォルトでsyslogに出力する。以下は出力先。  
/var/log/messages（redhat）  
/var/log/syslog（debian）  

<h3>ゾーンファイル</h3>



<h4>rndc（コマンド） </h4>
namedを制御するコマンドでBIND9より提供されている。  

・rndc status  
namedの状態を表示する。  
（systemctl status namedと記載は違う。）
・rndc halt  
namedを直ちに停止させる。
・rndc stop  
動的更新などを保存してからnamedを停止させる。  
・rndc reload  
設定ファイル（/etc/named.conf およびゾーンファイル）を再読み込みして有効化する。（問い合わせ等は失われない） 
他同様のコマンド    
/etc/init.d/named restart  (service named restart)  
kill -HUP `cat /var/run/named.pid`  

```
# pwd
/run/named
# ls -ltr
合計 8
-rw------- 1 named named 102  6月  8 09:09 session.key
-rw-r--r-- 1 named named   5  6月  8 09:10 named.pid
# ls -ltr /var/run/named
合計 8
-rw------- 1 named named 102  6月  8 09:09 session.key
-rw-r--r-- 1 named named   5  6月  8 09:10 named.pid
```

<H4>語彙</H4>

フォワーダ  
DNSサーバが自分のところに来た問い合わせの答えを知らない場合に他のDNSサーバへ転送すること  

DNSSEC（DNS Security Extensions）  
公開鍵暗号方式を用いた電子署名で正当なDNSサーバからのゾーン情報であることを認証、ゾーン情報が改ざんされていないことを保証する技術。  
dnssec-keygen -n ZONE（オプションのデフォルトがZONEのため指定不要）で作成。  
ゾーンファイルへの署名→dnssec-signzoneコマンド

TSIG（Transaction Signature）  
マスターDNSとスレーブDNSni共通の秘密鍵を設定し、
ゾーン転送により同期されるゾーンデータなどでデータ改ざんや、DNSサーバの偽装やなりすましを防ぐ技術  
(ゾーン情報の暗号化は行わない。)  
dnssec-keygen -n HOST で共有秘密鍵を作成。  

powerdns  
bindと同じunixで利用できるDNSのオープンソフトウェア。  
dnssecやbindのゾーンファイルの他、  
LDAPやRDB（リレーショナルデータベース、MYSQLやpostgresqlなど）の利用が可能。  

dnsmasq  
bindと同じunixで利用できるDNSのオープンソフトウェア。  
bindより設定が簡易で軽量。小規模DNSキャッシュサーバとして利用され、DHCPサーバの利用も可能である。  
設定ファイルは/etc/dnsmasq.conf  
始めに/etc/hostsファイルを参照し、名前解決できない場合は/etc/resolv.confに記載のDNSサーバに問い合わせを転送する。  
