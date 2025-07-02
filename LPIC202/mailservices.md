##  postfix  
sendmailと互換性があり、高機能で、使用方法が簡略化されている。  
###  postfixの設定ファイル  
-  /etc/postfix/main.cf  
postfixのMTA各種設定  
-  /etc/postfix/master.cf  
postfixのデーモンの設定  

##  dovecot  
IMAP/POP3サーバ  
メールボックスフォーマットはmbox Maildir  
imaps（993）pop3s（995）の各ポートのSSL/TLS接続  
標準のimaps（143）pop3（110）ポートに接続後に  
クライアントからのコマンドによってTLSによる暗号化通信を行うstarttls  
平文認証の他に、CHAM-MD5を含む多様な認証方式をサポート。
