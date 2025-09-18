##  samba  

SMB/CIFSによるwindows系ファイル共有システム  

###  smb.conf  
/etc/samba/smb.conf  
sambaサーバの設定ファイル。  

####  予約済みセクション  
-  samba変数  
%U セッションのユーザ名に置換される。
%m クライアントのNETBIOS名  
%v sambaバージョン番号  
1.[global]セクション  
samba全般の設定を行う。  
globalセクション以降のセクションで設定した項目が現れた場合は上書きする。（globalでの設定はその他セクションのデフォルト値になる）  
-  security  
認証方法を設定する。  
    -  user  ユーザレベルでの認証
    -  domain  NTドメインのドメインコントローラーを使用して認証
    -  tdb 
    trivial databaseでの認証。  
    sambaが内部で生成するバイナリ形式のデータベース。  
    -  ads  Active Directoryのドメインコントローラーで認証  
-  passdb backend = 認識方式  
securityがuserの場合に認証方式を指定する。（複数可）  
-  smb passwd file = ファイル名  
securityがuserの場合にsmbpasswdで使用するパスワードファイルを指定。  
-  guest account  
ゲストユーザのユーザ名を指定。  
-  encrypt passwords  yes/no  
暗号化されたパスワードを使用するかを指定する
-  server role (サーバの役割)  
sambaの動作モードを設定する。  
    -  member server  
ドメインのメンバーサーバになる。  
    -  active directory domain controller  
ADサーバのドメインコントローラーになる。  
    -  standalone  
スタンドアロンサーバになる。  
-  map to guast  
ユーザが認証できなかった場合のゲスト認証動作を設定する。  
    -  Never  ゲスト認証しない  
    -  Bad Password  パスワードミスでもゲスト認証する。  
    -  Bad User  存在しないユーザの場合はゲスト認証する。  
-  workgroup  
ADのNetBios名を指定。ADドメインに参加させるために必要  
-  netbios name  
sambaサーバ自身のNetBios名を指定する項目。  
-  realm  
activedirectryのドメイン名（realm）を指定。 ADドメインに参加させるために必要
-  log file ファイル名  
ログ出力するファイルを指定。変数%m指定で接続ホストごとのログファイルを生成する。  
-  log level  
ログレベルを指定する。  
-  max log size  
ログファイルの最大値を指定。最大値を超えると新しいファイルを作成する。  
-  logon script = スクリプト名  
ログインした際に実行するスクリプトファイルを指定する。
-  wins server （ipアドレス） 
winsサーバーのipを指定する。  
-  wins support  yesno
sambaサーバーをwinsサーバーとしても動作させるかどうかを指定する。  
-  os level = 数値（最大255）  
どのサーバがマスターブラウザになるかを決める指標。
-  local master = yesno  
sambaにマスターブラウザの選挙に参加させる。
-  preferred master = yesno  
マスターブラウザ選挙要求をネットワークにブロードキャストする。  
-  unix password sync = yesno  
linuxシステムとsambaのパスワードを同期する。（linuxとsambaでは異なる）  

2.[homes]セクション  
unixユーザの各ホームディレクトリを一括して共有するためのセクション。  

3.[printers]セクション  
共有プリンタに関する設定を行う。  
プリンタが複数ある場合でも、プリンタごとに共有設定をする必要はない。  

-  print ok =yes, printable = yes  
共有プリンタ設定を有効化する。  

[homes].[printers].その他セクション共通の設定項目  


-  セクション名[kyoyu]などの名前にした場合、windows上で共有フォルダ「kyoyu」として表示される。  
-  writeable[yes|no]  
   書き込みを許可するかどうかを設定  
-  writelist=[ユーザ名|@グループ名]  
   writeableで書き込みが禁止されていても、書き込みができるユーザを指定する。  
-  hide files =  
表示させないファイルを指定。アクセスは可能。
-  veto files =  
表示させないファイルを指定。アクセス不可。
-  comment  
ブラウジングした際に表示される文章。  
-  browseable  
共有がクライアントのネットワーク一覧（エクスプローラなど）に表示されるかどうかを指定する。
-  force [user/group]  
共有内に作成するファイルディレクトリの所有ユーザ/グループを指定のものに強制する。  
-  guest ok | はpublic = yesno  
ゲストユーザ→パスワードなしでのログインをするユーザ  
-  [create|Directory] mask = ファイルorディレクトリに適用可能なパーミッションを設定。  
-  force [create|Directory] mask =  必ずファイルorディレクトリに適用可能なパーミッションを設定。


###  デーモン

-  smbd(port TCP139 TCP445)  
ファイル・プリンタの共有やユーザ認証などのSambaの基本的な機能を提供する。  

-  winbind  
sambaサーバへアクセスする際にwindowsドメインのユーザでの認証をできるようにする。  
（既存あるいは新規作成のlinuxユーザと紐付け→NSS（/etc/nsswitch.conf）を利用してユーザ情報を取得。）  
デーモン→winbindd  

-  idmap  
windowsのsidとlinuxのuid,gidとのマッピングする機能。  
    -  idmap config * : backend  
    idmap機構で使用する、backend（tdb,ldapなど）の指定。  
    -  idmap config * : range
    Linuxユーザに割り当てるUIDGID範囲を設定する。

-  samba4からADのドメインコントローラーの機能追加され、起動コマンドはsystemctl start samba  
-  samba4よりDNSによる名前解決機能を導入。
-  ファイルサービスについて、samba3までSMB2に対応し、samba4よりSMB2，3に対応。  


###  sambaコマンド  
sambaユーザの管理を行うことができるコマンド。  
smbpasswdもpdbeditも追加するsambaユーザと同名のlinuxユーザが必要。
-  smbpasswd  
    テキスト形式ファイル/etc/samba/smbpasswd  
    -  -a sambaユーザを追加する。-e　有効化　-d  無効化　-x 削除  
-  pdbedit
    -  -L sambaユーザの一覧を表示する。　-a　追加　-x　削除  
-  samba-tool  サブコマンド  
    ドメインやユーザ、セキュリティなどの管理を行うことができる。
    -  testperm  
    設定ファイルの構文チェックを行う。  
    -  user  
    ユーザ管理を行う  
-  smbclient  
    windowsネットワーク上の共有リソースにアクセスできる。  
    -  -L ホスト名/--list ホスト名  指定ホストで共有可能なリソースを一覧表示
    -  -U ホスト名/--user ホスト名  接続するユーザを指定
    -  -N 接続を行わない
-  testparm
    smb.confの構文チェックを行う。  
    -  -s 構文チェックの後に自動的にsmb.confの内容を表示する。
    -  -v smb.confに記載されていない設定を表示する。
-  smbstatus  
    sambaに接続されているユーザやロックされているファイルなどの情報確認する。  
-  smbcontrol  
smbd nmbd winbinddなどのデーモンにメッセージを送ることができる。  
クライアントとの接続をkillするなど。  

```
# systemctl start smb nmb
# smbcontrol all ping
PONG from pid 3257
PONG from pid 3261
PONG from pid 3256
PONG from pid 3262
PONG from pid 3263
# systemctl stop smb nmb

```

-  nmblookup  
    NETBIOS名からIPアドレスを参照したり、その逆の参照もできる。マスターブラウザを検索もできる。  
    - Aオプション  
    IPアドレス↔macアドレスやNETBIOS名の参照ができる。  
    - Mオプション  
    指定したワークグループのマスターブラウザを検索する。  

###  その他  
-  lmhosts  
NETBIOS名とIPアドレスの紐付けが記載されるファイル。  
（/etc/samba/lmhosts）  
NETBIOS名とIPアドレスの名前解決にはWinsサーバが使用されるが、解決できない場合はlmhostsが使用される。  
-  winsサーバー  
NETBIOS名とIPアドレスの名前解決を行う。

##  NFS  
UNIX標準のファイル共有  
nfsクライアントがリモートファイルシステムをマウントすることで、ローカルファイルシステムのように扱うことができる。  

###  /etc/exports  
NFSの設定ファイル。  
特定のディレクトリを公開することをエクスポートという。  
そのエクスポートしたいディレクトリを記載する。  
■設定オプション  
-  no_root_squash  
   rootリクエストの場合はroot権限でのアクセスを許可。（匿名ではない）  
-  sync  
　　ディスクへの書き込み後に応答を返す  
-  async
　　ディスクへの書き込み前に応答を返す。パフォーマンス改善されるが、書き込み完了前に再起動が走るとエラーの原因になる。  

■コマンド
-  exportfs  
指定のディレクトリのエキスポート状況の確認やエキスポート、アンエキスポートができる。  
デフォルト（オプション何もなし）ではエキスポート状況を確認できる。  
    - -u ディレクトリをアンエキスポートする　-a全てエキスポート　-r  全て再エキスポートする  
-  nfsstat  
NFSやRPCの統計情報について出力する。  
-  rpcinfo  
RPCの情報について出力。  
-  showmount  
NFSサーバを利用しているクライアントを調べる。  

■その他  
-  /etc/fstab  
ブート時にマウントしたいファイルシステムを指定。  
NFSでも有効で、NFSクライアントで設定することで、ブート時にマウントできる。  

###  RPC  
リモートホストにある機能を他のホストが使用できるようにする仕組み。  
RPCプログラムにはそれぞれRPCプログラム番号があり、/etc/rpcに記載がある。  
RPCプログラムのtcpudpポート番号は動的に割り当てされている。  
RPCプログラム番号とポート番号とのマッピングはportmapで制御されている。  

rpcinfo（コマンド）  
RPCサービスの状況を確認できる。  

###  mount
NFSクライアントからNFSサーバでエクスポートされているファイルシステムをマウントするにはmountコマンドを使用することができる。 
- -t ファイルシステムを指定する。  
    -  cifs samba4の共有ディレクトリをLinux上でマウントするにはCIFSをプロトコルに使用する。

-  softマウント  
NFSサーバから応答がなければタイムアウトしてプログラムを終了させる。
    -  retruns  
       タイムアウトした際に試行回数を指定。  

-  hardマウント  
NFSサーバから応答があるまで再試行を続ける。
    -  intr  ハードマウントで割り込みを受け付ける。  

-  nolock  
ファイルロックをかけないようにする。  
アクセスしたファイルの使用を１人のユーザあるいはプロセスだけの使用にしないようにする。  

###  NFSアクセス制御  
書式
-  tcp wrapperを使用（/etc/hosts.allow）
portmap:192.168.1.  
NFSv3まではrpcbindを使用している。  



##  kerberos  
ユーザ認証システム。Activedirectoryで使用する。  
クライアントはサービスを受けたいサーバに接続する前に、  
kerberosサーバに接続し、サーバとの通信で使用するチケットを受け取り、それを使用してサーバにアクセスする。  
kerberosサーバとユーザ間の通信、サーバとユーザ間の通信は暗号化されている。  

##  語彙  
-  realm  
kerberos認証システムにより管理される領域のこと。smb.confではADサーバのドメイン名を指定する。  
-  workgroup
windowsネットワークと通信するための論理グループで、グループ内でのファイル共有やプリンタ共有を可能にする。  
-  ドメインコントローラー  
ユーザ認証の機能を司り、ユーザ情報を管理統括する。  
ユーザログインの際に認証、どのファイルディレクトリにアクセスできるかを管理など  
-  マスターブラウザ  
windowsのネットワーク上のpc一覧を管理し、問い合わせがあったネットワーク上のpcの情報提供をする。

