<h1>lpic202</h1><br>

<h3>DNS</h3>
<br>
bind<br>
linuxのdnsソフトウェア<br><br>
named<br>
bindにおいて、dnsのサービスを提供するデーモン。<br>
/usr/sbin/named<br>
起動すると最初に/etc/named.confを読み込む<br><br>
named-checkconf<br>
/etc/name.confの書式誤りをチェックする。<br>
オプション-zでゾーンファイルの内容もチェックする。<br>

```
# named-checkconf
/etc/named.conf:20: unknown option '??'
```

<br>

フォワーダ  
DNSサーバが自分のところに来た問い合わせの答えを知らない場合に他のDNSサーバへ転送すること<br><br>

rndc（コマンド）  

namedを制御するコマンドでBIND9より提供されている。  

rndc reload  

設定ファイルを再読み込みして有効化する。


