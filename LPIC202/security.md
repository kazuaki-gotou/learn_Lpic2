###  kernel_param

####  sysctl  
/proc/sysディレクトリ配下にあるカーネルパラメータファイルを表示、設定できる  
sysctlコマンドで設定した値は再起動すると消去される。  
→/etc/sysctl.confに設定を記述すると設定が有効になる。  
  （システム起動時にsysctl -p（/etc/sysctl.confの設定を有効にする）が実行されるため。）  
-  /proc/sys/net/ipv4/tcp_syncookies  
tcpセッションの正当性確認の設定の有効無効を設定。tcpflood攻撃の対策。  
echo 1 > /proc/sys/net/ipv4/tcp_syncookiesにて有効にする。（無効化する場合は0）  
-  /proc/sys/net/ipv4/ip_forward  
異なるNW間でのパケットの転送を有効化する。

```確認
$ pwd
/proc/sys/net/ipv4
$ more ip_forward
1
```

###  iptables  
NATやACLを設定できる。  
linuxカーネルにnetfilter機能がありそれを利用するコマンド。  
-  テーブル  
いくつかのチェインのセットのこと  
    -  filter フィルタリング  
    INPUT OUTPUT FORWARDが使用可能  
    -  nat  アドレス変換  
    INPUT OUTPUT PREROUTING POSTROUTINGが使用可能  
    -  mangle  パケットのヘッダの書き換えを行う  
    -  raw コネクションの追跡を行わない  
-  チェイン  
パケットを検査するための1連のルールセット。  
    -  forward  ローカルホストから経由される通信を制御
    -  output  
    ローカルホストから送信される通信を制御

####  iptablesコマンド  
-  F  チェインの中のルールを全削除  
-  X  ルールを保持していないチェインを削除。チェイン名を指定しない場合はルールを保持しない全チェインを削除。ルールを保持している場合は削除できない。  
-  P  ポリシーを設定する。（フィルタリングに引っ掛からない場合の動作を設定する。）  
-  A  ルール（チェイン）を追加する。  
-  L  iptablesでの設定を一覧表示。  
-  iptables-restore < ファイル名  
保存しているiptables設定ファイルをリストアする。要標準入力。  
-  iptables-save  
ルールのバックアップや一覧表示に使用する。  
ipv6ではip6tables-saveコマンド  
-  iptables -t nat -A POSTROUTING ルール　
IPマスカレード　NAPTの設定コマンド  
-  -j ターゲットの指定（logなど）　-p プロトコルの指定（tcpudp）　-o/-i 送信/受信するIFを指定  

####  fail2ban  
ログを監視し指定の文字列を検知した場合は該当のIPアドレスからのアクセスを遮断する。  
アクセス制御発動条件をjailと呼ぶ。  
アクセスの遮断にはiptablesを使用し、自動でアクセス制限解除を行う。  

###  FTP  
-  ACTIVE（アクティブモード）  
データ転送用のポートの接続要求をサーバ側から行う。データ転送TCP20 データ制御TCP21
-  PASV（パッシブモード）  
データ転送用ポートの接続要求をクライアント側から行う。  データ転送は適当なTCPポート番号。データ制御はTCP21
サーバ側も対応している。  
####  設定ファイル  
-  /etc/ftpusers  
ftpサーバにログインを禁止するユーザを指定する。  
1行ごとにユーザ名を記載することで指定が可能になる。  
####  Pure-ftpd  
強固なセキュリティと実用性に主眼を置いたFTPサーバ  
設定はpure-ftpd.confで行う。  
起動時オプション  
-  e 匿名ユーザのみログイン許可する。/E　匿名ユーザ利用不可  
-  i 匿名ユーザによるアップロード不可

####  vsftpd  
セキュリティが高く、パフォーマンスに優れたFTPサーバ。  
多くのディストリビューションでFTPサーバとして標準採用されている。設定ファイルはvstfp.conf
####  pure-ftpd  
よく使われるFTPサーバ  
-  pure-ftpd.conf  
設定ファイル  

###  SSH  
####  openssh  
#####  /etc/ssh/sshd_config  
sshd（opensshのデーモン）の設定ファイル。  
-  Protocol  
受け付けるSSHバージョンを指定できる。デフォルトは2
-  AllowUsers　ユーザ名  
接続許可するユーザを指定  
デフォルトでは全てのユーザのログインが許可されており、  
この設定がある場合は許可されたユーザ以外を拒否する。  
-  DenyUsers　ユーザ名  
接続拒否ユーザを指定 denyとallowが両方ある場合はdenyが優先される。  
-  X11Forwarding yes/no  
X11（xwindow）の通信を許可拒否　デフォルトはNO  
-  ~/.ssh/known_host  
ユーザ認証のためにリモートホスト（サーバ）でユーザ（クライアント）の公開鍵を保存。  
-  ~/.ssh/authorised_keys  
ホスト認証の為にユーザでリモートホストの公開鍵を保存し、接続する際に出力される公開鍵と照合する。

#### SCP
通信を暗号化してリモートホストとのファイルの交換を行う。  
書式  
scp -P [portnum] -r(再帰的にファイル指定)　送信したいデータのパス　送信先ユーザ名@ホストIP:送信先ディレクトリパス

####  ssh-agent
クライアントからログインで公開鍵認証の際に毎回パスフレーズを聞かれることを防ぐ認証エージェント。メモリ上に秘密鍵を常駐させることで、パスフレーズ確認無しでログイン可能。  
-  ssh-add  
ssh-agentに秘密鍵とパスフレーズを追加する。

###  openvpn  
linuxでvpn機能を提供するアプリケーション。  
/etc/openvpn/server.confに設定ファイルがある。  

##  nmap
ポートスキャンのコマンド  
-  スキャンタイプ
    - -sS TCPのSYNスキャン（デフォルト）
    - -sT TCPconnectスキャン
    - -sU UDPスキャン
-  オプション
    - -F ウェルナウンポートを対象にすることでスキャン速度を高速化  
    - -O 相手のホストOSを検出する

```Ubuntuからのnmap
nmap IPアドレス
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-08-11 09:41 JST
Nmap scan report for IPアドレス
Host is up (0.018s latency).
Not shown: 977 filtered tcp ports (no-response), 18 filtered tcp ports (host-unreach)
PORT     STATE  SERVICE
53/tcp   open   domain
80/tcp   open   http
139/tcp  open   netbios-ssn
445/tcp  open   microsoft-ds
2049/tcp closed nfs
```

##  ナレッジ  
-  CERT  
セキュリティインシデントを受け付け、技術支援などを行う。  
    -  JPCERT  日本国内のCERT組織  

-  Tripwire  
ファイルディレクトリの状態を監視し、ファイルの改ざんを検知するツール。  