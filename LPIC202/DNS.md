<h1>DNS</h1>
  
bind  
linuxのdnsソフトウェア  

named  
bindにおいて、dnsのサービスを提供するデーモン。  

<h3>named.conf</h3>  

BIND（named）の設定ファイル。/etc/named.conf  
namedの動作に関する詳細な設定を行う。

<h4>optionsステートメント</h4>  

・directory  
namedの作業ディレクトリ

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

/usr/sbin/named  
起動すると最初に/etc/named.confを読み込む    
named-checkconf  
/etc/name.confの書式誤りをチェックする。  
オプション-zでゾーンファイルの内容もチェックする。  

```
# named-checkconf
/etc/named.conf:20: unknown option '??'
```

rndc（コマンド）  
namedを制御するコマンドでBIND9より提供されている。  
rndc reload  
設定ファイルを再読み込みして有効化する。 


用語  

フォワーダ  
DNSサーバが自分のところに来た問い合わせの答えを知らない場合に他のDNSサーバへ転送すること  



