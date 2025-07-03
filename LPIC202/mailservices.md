##  postfix  
sendmailと互換性があり、高機能で、使用方法が簡略化されている。  
###  postfixの設定ファイル  
-  /etc/postfix/main.cf  
postfixのMTA各種設定  
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

##  dovecot  
IMAP/POP3サーバ  
メールボックスフォーマットはmbox Maildir  
imaps（993）pop3s（995）の各ポートのSSL/TLS接続  
標準のimaps（143）pop3（110）ポートに接続後に  
クライアントからのコマンドによってTLSによる暗号化通信を行うstarttls  
平文認証の他に、CHAM-MD5を含む多様な認証方式をサポート。

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
