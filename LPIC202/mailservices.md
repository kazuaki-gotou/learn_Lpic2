##  Protocol  
メール送受信で使用されるプロトコル。
###  IMAP
メール受信で使用されるプロトコル。
サーバ上にあるメールを管理し、複数の端末からアクセスできるようにする。  
port 143 IMAPS 993
###  POP3
メール受信にて使用されるプロトコル。 
受信するとサーバ上のメールは削除される。 
port 110 POP3S 995
###  SMTP  
メール送信にて使用されるプロトコル。  
port 25 SMTPS 465   
##  postfix  
sendmailと互換性があり、高機能で、使用方法が簡略化されている。  
###  postfixの設定ファイル  
-  /etc/postfix/main.cf  
postfixのMTA各種設定  
    -  mydomain  メールサーバーのドメインを設定する。 
    -  relay_domains  許可されたクライアント以外からのメールで中継を許可するドメインを設定。  
    -  smtpd_use_tls  yesでTLSを有効化する。  
    -  smtpd_enforce_tls  yesでTLSを強制する。  
    TLSが有効化されていない場合はこの設定も無効になる。（設定依存）  
    -  mail_spool_directory  メールスプール先を指定  
        -  mbox  ユーザごとに1ファイルに保存
        -  Mairdir  1メール1ファイルに保存  
    -  inet_interfaces メールをどのIPアドレスで受信するかを設定
    -  canonical_maps 送受信アドレスの書き換えで参照する  
        -  sender_canonical_maps 送信アドレスを書き換える　recipient_canonical_maps 受信アドレスを書き換える  
    -  mydestination 指定したホスト宛のメールをローカルのメールボックスに配送する  
    -  myorigin 宛先をユーザ名にした場合@以降のドメインを補完する。  
-  /etc/postfix/master.cf  
postfixのデーモンの設定  

###  コマンド  
-  postconf  
postfixの設定内容を確認できる。  
    -  -n  デフォルトの設定との差分を出力する。  
-  postfix  
postfixをコントロールするコマンド。
    -  check postfix設定ファイルの構文チェックをする。   
    -  stop postfixを適切に停止する。  
    -  abort postfixを強制停止する。  
    -  flush メールキュー内のメールを直ちに再送する。  
##  sendmail  

###  sendmailの設定ファイル  
/etc/mailディレクトリ配下に設定ファイルを配置。  

##  dovecot(ダブコット)  
IMAP/POP3対応のMDA（届いたメールをユーザの受信箱に入れる）  
メールボックスフォーマットはmbox Maildir  
imaps（993）pop3s（995）の各ポートのSSL/TLS接続  
標準のimaps（143）pop3（110）ポートに接続後に  
クライアントからのコマンドによってTLSによる暗号化通信を行うstarttls  
平文認証の他に、CHAM-MD5を含む多様な認証方式をサポート。  

###  dovecot.conf  
メインの設定ファイル  
/etc/dovecot/conf.dにもコンフィグがあり、  
IMAPについては20-imap.conf  POP3は20-pop3.conf SSLは10-ssl.conf  
-  listen  接続を待ち受けるIPアドレスを設定する。  
-  mechanisms  
dovecotの認証方式について設定する。  
-  mail_max_userip_connections  
一つのIPアドレスの1人のユーザからの最大接続数を設定する。  
ユーザについては大文字小文字で区別される。  

```
  # Maximum number of IMAP/POP3 connections allowed for a user from each IP address.
  # NOTE: The username is compared case-sensitively.
```

###  /etc/dovecot/conf.d
-  10-ssl.conf
SSLについての設定を行う。  
    -  ssl yes/no 有効無効　required 必須  
    -  ssl_cert（dovecot1.0はssl_cert_file）  
       ssl証明書が配置されているパス。  
###  コマンド  
-  doveadm  dovecotの管理コマンド。  
    -  who ログイン中のユーザを表示
    -  pw 新規パスワードを生成

###  Sieve  
メールのフィルタリングにて使用されるプログラミング言語。  
dovecotやサーバサイドのメールソフトで使用される。 

####  構文
■コントロール  
-  require 拡張機能を設定  

■テスト  
■アクション  
 

###  /var/spool  
一時的に作業用などで配置するディレクトリ。  
配下にmailやprinterなどのサーバが使用。

##  knowledge  
- /etc/aliases  
メールアドレスの別名について設定できる。(設定後はnewaliasesで更新する。)  
    -  メーリングリスト
    メールを送付するリストをファイルで管理して、aliasesファイルにインポートする。  
    書式  
    mailinglist(ここを宛先にするとファイルのユーザに送付可能):　:include:/etc/mail/userlist  
- exim  
debian標準のMTA　
-  Coutier-IMAP  
maildirを使用したIMAPサーバ  

