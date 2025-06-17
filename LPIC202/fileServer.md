##  samba  

SMB/CIFSによるwindows系ファイル共有システム  

####  smb.conf  
/etc/samba/smb.conf  
sambaサーバの設定ファイル。  

  
##  nfs  
UNIX標準のファイル共有  

###  kerberos  
ユーザ認証システム。  
クライアントはサービスを受けたいサーバに接続する前に、  
kerberosサーバに接続し、サーバとの通信で使用するチケットを受け取り、それを使用してサーバにアクセスする。  
kerberosサーバとユーザ間の通信、サーバとユーザ間の通信は暗号化されている。  
realm  
kerberos認証システムにより管理される領域のこと。  

