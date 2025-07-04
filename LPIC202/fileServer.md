##  samba  

SMB/CIFSによるwindows系ファイル共有システム  

###  smb.conf  
/etc/samba/smb.conf  
sambaサーバの設定ファイル。  

####  予約済みセクション  
1.[global]セクション  
samba全般の設定を行う。  
globalセクション以降のセクションで設定した項目が現れた場合は上書きする。（globalでの設定はその他セクションのデフォルト値になる）  

-  security  
認証方法を設定する。  
    -  user  ユーザレベルでの認証
    -  domain  NTドメインのドメインコントローラーを使用して認証
    -  ads  Active Directoryのドメインコントローラーで認証  
-  passdb backend = 認識方式  
securityがuserの場合に認証方式を指定する。（複数可）  
-  smb passwd file = ファイル名  
securityがuserの場合にsmbpasswdで使用するパスワードファイルを指定。
-  server role (サーバの役割)  
sambaの動作モードを設定する。  
    -  member server  
ドメインのメンバーサーバになる。  
    -  active directory domain controller  
ADサーバのドメインコントローラーになる。  
    -  standalone  
スタンドアロンサーバになる。  

-  realm  
activedirectryのドメイン名（realm）を指定。  

-  wins server （ipアドレス） 
winsサーバーのipを指定する。  
-  wins support  
sambaサーバーをwinsサーバーとして動作するかどうかを指定する。  

2.[homes]セクション  
unixユーザの各ホームディレクトリを一括して共有するためのセクション。  

3.[printers]セクション  
共有プリンタに関する設定を行う。  
プリンタが複数ある場合でも、プリンタごとに共有設定をする必要はない。  

-  print ok =yes, printable = yes  
共有プリンタ設定を有効化する。  

2.3.その他セクション共通の設定項目  

-  writeable[yes|no]  
   書き込みを許可するかどうかを設定  
-  writelist=[ユーザ名|@グループ名]  
   writeableで書き込みが禁止されていても、書き込みができるユーザを指定する。

###  デーモン

-  smbd(port TCP139 TCP445)  
ファイル・プリンタの共有やユーザ認証などのSambaの基本的な機能を提供する。  

-  winbind  
sambaサーバへアクセスする際にwindowsドメインのユーザでの認証をできるようにする。  
（既存あるいは新規作成のlinuxユーザと紐付け→NSS（/etc/nsswitch.conf）を利用してユーザ情報を取得。）  
デーモン→winbindd  

    -  idmap  
       windowsのsidとlinuxのuid gidとのマッピングする機能。  

-  samba4からADのドメインコントローラーの機能追加され、起動コマンドはsystemctl start samba  
-  ファイルサービスについて、samba3までSMB2に対応し、samba4よりSMB2，3に対応。  

###  sambaコマンド  
sambaユーザの管理を行うことができるコマンド。  
-  smbpasswd  
    テキスト形式ファイル/etc/samba/smbpasswd  
    -  -a sambaユーザを追加する。-e　有効化　-d  無効化　-x 削除  
-  pdbedit
    -  -L sambaユーザの一覧を表示する。　-a　追加　-x　削除  
-  samba-tool  サブコマンド  
    ドメインやユーザ、セキュリティなどの管理を行うことができる。
    -  testperm  
    設定ファイルの構文チェックを行う。
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
smbd nmbd winbinddにメッセージを送ることができる。  
クライアントとの接続をkillするなど。  

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

■コマンド
-  exportfs  
指定のディレクトリのエキスポート状況の確認やエキスポート、アンエキスポートができる。  
デフォルト（オプション何もなし）ではエキスポート状況を確認できる。  
    - -u アンエキスポートする　-a全てエキスポート　-r  全て再エキスポートする  
-  nfsstat  
NFSやRPCの統計情報について出力する。  
-  rpcinfo  
RPCの情報について出力。  

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

-  softマウント  
NFSサーバから応答がなければタイムアウトしてプログラムを終了させる。
    -  retruns  
       タイムアウトした際に試行回数を指定。  

-  hardマウント  
NFSサーバから応答があるまで再試行を続ける。  

##  kerberos  
ユーザ認証システム。Activedirectoryで使用する。  
クライアントはサービスを受けたいサーバに接続する前に、  
kerberosサーバに接続し、サーバとの通信で使用するチケットを受け取り、それを使用してサーバにアクセスする。  
kerberosサーバとユーザ間の通信、サーバとユーザ間の通信は暗号化されている。  

###  realm  
kerberos認証システムにより管理される領域のこと。  

##  語彙  
-  ドメインコントローラー  
ユーザ認証の機能を司り、ユーザ情報を管理統括する。  
ユーザログインの際に認証、どのファイルディレクトリにアクセスできるかを管理など  
-  マスターブラウザ  
windowsのネットワーク上のpc一覧を管理し、問い合わせがあったネットワーク上のpcの情報提供をする。

