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