##  samba  

SMB/CIFSによるwindows系ファイル共有システム  

###  smb.conf  
/etc/samba/smb.conf  
sambaサーバの設定ファイル。  

####  予約済みセクション  
1.[global]セクション  
samba全般の設定を行う。  
globalセクション以降のセクションで設定した項目が現れた場合は上書きする。  

-  security  
認証方法を設定する。  
    -  user  ユーザレベルでの認証
    -  domain  NTドメインのドメインコントローラーを使用して認証
    -  ads  Active Directoryのドメインコントローラーで認証
-  server role (サーバの役割)  
sambaの動作モードを設定する。  

    -  member server  
ドメインのメンバーサーバになる。  

    -  active directory domain controller  
ADサーバのドメインコントローラーになる。  

    -  standalone  
スタンドアロンサーバになる。

2.[homes]セクション  
unixユーザの各ホームディレクトリを一括して共有するためのセクション。  

3.[printers]セクション  
共有プリンタに関する設定を行う。  
プリンタが複数ある場合でも、プリンタごとに共有設定をする必要はない。  

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

###  sambaコマンド  
sambaユーザの管理を行うことができるコマンド。  
-  smbpasswd  
    テキスト形式ファイル/etc/samba/smbpasswd  
    -  -a sambaユーザを追加する。
-  pdbedit
    -  -L sambaユーザの一覧を表示する。  

####  samba確認コマンド  
-  smbstatus  
sambaに接続されているユーザやロックされているファイルなどの情報確認する。  
-  nmblookup  
NETBIOS名からIPアドレスを参照したり、その逆の参照もできる。マスターブラウザを検索もできる。  

    - Aオプション  
    IPアドレス↔macアドレスやNETBIOS名の参照ができる。  
    - Mオプション  
    指定したワークグループのマスターブラウザを検索する。  

##  nfs  
UNIX標準のファイル共有  

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

