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
IMAPについては20-imap.conf  POP3は20-pop3.confにある。  
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

###  コマンド  
-  doveadm  dovecotの管理コマンド。  


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
