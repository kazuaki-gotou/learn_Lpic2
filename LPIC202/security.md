###  kernel_param

####  sysctl  
/proc/sysディレクトリ配下にあるカーネルパラメータファイルを表示、設定できる  
sysctlコマンドで設定した値は再起動すると消去される。  
→/etc/sysctl.confに設定を記述すると設定が有効になる。  
  （システム起動時にsysctl -p（/etc/sysctl.confの設定を有効にする）が実行されるため。）  

###  iptables
-  テーブル  
いくつかのチェインのセットのこと  
    -  filter フィルタリング  
    -  nat  アドレス変換  
    -  mangle  パケットのヘッダの書き換えを行う  
    -  raw コネクションの追跡を行わない  
-  チェイン  
パケットを検査するための1連のルールセット。  

####  コマンド  
-  L  iptablesでの設定を一覧表示。  
-  iptables-restore < ファイル名  
保存しているiptables設定ファイルをリストアする。要標準入力。  


###  FTP

###  SSH  
####  openssh  
#####  /etc/ssh/sshd_config  
sshd（opensshのデーモン）の設定ファイル。  
-  AllowUsers　ユーザ名  
接続許可するユーザを指定  
デフォルトでは全てのユーザのログインが許可されており、  
この設定がある場合は許可されたユーザ以外を拒否する。  
-  DenyUsers　ユーザ名  
接続拒否ユーザを指定 denyとallowが両方ある場合はdenyが優先される。  
-  X11Forwarding yes/no  
X11（xwindow）の通信を許可拒否  

##  ナレッジ  
-  CERT  
セキュリティインシデントを受け付け、技術支援などを行う。  
    -  JPCERT  日本国内のCERT組織  
