<h3>kernel_param</h3>

sysctl  
/proc/sysディレクトリ配下にあるカーネルパラメータファイルを表示、設定できる  
sysctlコマンドで設定した値は再起動すると消去される。  
→/etc/sysctl.confに設定を記述すると設定が有効になる。  
  （システム起動時にsysctl -p（/etc/sysctl.confの設定を有効にする）が実行されるため。）  

<h3>iptables</h3>
<h5>パケットフィルタリング</h5>

・チェイン  
パケットを検査するための1連のルールセット。  
（ciscoルータの名前付きアクセスリストのようなもの？）


<h3>FTP</h3>

<h3>SSH</h3>