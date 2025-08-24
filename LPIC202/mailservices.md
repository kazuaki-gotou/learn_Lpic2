##  Protocol  
メール送受信で使用されるプロトコル。  
https://ascii.jp/elem/000/000/439/439105/
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
####  SMTPコード  
-  2xx 成功  
   -  220  サービスの準備が完了
   -  221  送信チャネルを閉じる
   -  235  認証が成功
   -  250  要求された処理が完了  
##  postfix  
sendmailと互換性があり、高機能で、使用方法が簡略化されている。  
###  postfixの設定ファイル  
-  /etc/postfix/main.cf  
postfixのMTA各種設定  
    -  mydomain  メールサーバーのドメインを設定する。 
    -  relay_domains  許可されたクライアント以外からのメールで中継を許可するドメインを設定。  
    -  relayhost  配送の中継サーバを指定する。　　
    FWなどでインターネットへ抜けられない場合などでメールサーバを指定することでメール配送を可能とする。　　
    -  smtpd_use_tls  yesでTLSを有効化する。(2.3より前)  
    -  smtpd_enforce_tls  yesでTLSを強制する。(2.3より前)  
    TLSが有効化されていない場合はこの設定も無効になる。（設定依存）  
    -  smtpd_tls_security_level  
    postfix2.3以降で使用。tlsの使用について指定できる。may有効　encrypt強制  
    -  mail_spool_directory  メールスプール先を指定  
        -  mbox  ユーザごとに1ファイルに保存
        -  Mairdir  1メール1ファイルに保存  
    -  inet_interfaces メールをどのIPアドレスで受信するかを設定  
    -  mailbox_command ローカル配送をするMDA（プログラム）を指定する。  
    -  canonical_maps 送受信アドレスの書き換えで参照する  
        -  sender_canonical_maps 送信アドレスを書き換える　recipient_canonical_maps 受信アドレスを書き換える  
    -  mydestination 指定したホスト宛のメールをローカルのメールボックスに配送する  
    -  myorigin 宛先をユーザ名にした場合@以降のドメインを補完する。  
    -  disable_vrfy_command  
    SMTPコマンドのVRFYの有効無効を指定する。  
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
-  smtp  
メールの送受信の制御を行うコマンド。  
    -  リモートのサーバやクライアントがユーザ（postfix）のメールアカウントが存在するか確認するコマンド。

##  sendmail  

###  sendmailの設定ファイル  
/etc/mailディレクトリ配下に設定ファイルを配置。  

###  コマンド  
-  sendmail  
    - q メールキューの中身を送信　bp メールキューの中身を確認  
-  mailq  
postfixとsendmailのメールキューの中身を確認
-  postqueue  
postfixのキュー管理コマンド。  
-f キューの中にあるメールをすぐ送信する。-q メールキューの中身を確認。mailqと同じ
-  postsuper -d  
postfixのメールキューを削除。  

##  dovecot(ダブコット)  
IMAP/POP3対応のMDA（届いたメールをユーザの受信箱に入れる）  
メールボックスフォーマットはmbox Maildir  
imaps（993）pop3s（995）の各ポートのSSL/TLS接続  
標準のimaps（143）pop3（110）ポートに接続後に  
クライアントからのコマンドによってTLSによる暗号化通信を行うstarttls  
平文認証の他に、CHAM-MD5を含む多様な認証方式をサポート。  
SMTP（メール送信プロトコル）については非対応。

###  dovecot.conf  
メインの設定ファイル  
/etc/dovecot/conf.dにもコンフィグがあり、  
IMAPについては20-imap.conf  POP3は20-pop3.conf SSLは10-ssl.conf  
-  listen  接続を待ち受けるIPアドレスを設定する。  
-  mechanisms  
dovecotの認証方式について設定する。 
-  mail_location  
メール格納方法や格納場所について指定。  
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
    -  ssl_key（dovecot1.0はssl_key_file） 
       サーバ秘密鍵のパス  

###  コマンド  
-  doveadm  dovecotの管理コマンド。  
    -  who ログイン中のユーザを表示
    -  pw 新規パスワードを生成
    -  config コンフィグを表示  
###  Sieve  
メールのフィルタリングにて使用されるプログラミング言語。  
dovecotやサーバサイドのメールソフトで使用される。 

####  構文
■コントロール  
-  require 拡張機能を設定  

■テスト  
■アクション  
- reject  受信したメールの配送を拒否、送信元にエラーメッセージを返送する。  

##  knowledge
- /var/spool  
一時的に作業用などで配置するディレクトリ。  
配下にmailやprinterなどのサーバが使用。  
    - /var/spool/mail  
    受信したメールを格納する rootであればrootユーザに届いたメールを格納する。
    - /var/spool/mqueue  
    メールキュー（送信待ちメール）を格納
- /etc/aliases  
メールアドレスの別名について設定できる。(設定後はnewaliasesで更新する。)   
書式　admin: test;example
    -  メーリングリスト
    メールを送付するリストをファイルで管理して、aliasesファイルにインポートする。  
    書式  
    mailinglist(ここを宛先にするとファイルのユーザに送付可能):　:include:/etc/mail/userlist  
    -  コマンド引き渡し  
    admin: |/usr/local/bin/example  
    届いたメールを/usr/local/bin/exampleコマンドに引き渡し

- exim  
debian標準のMTA　
-  procmail  
MTAが受信したメールをそれぞれのユーザに配送するMDA
-  Coutier-IMAP  
maildirを使用したIMAPサーバ  
-  mbox  
/var/mail内で1ユーザの全てのメールを1つのファイルにする。  
-  maildir  
サブディレクトリに分類し、ユーザのホームディレクトリに1メールを1ファイルとして保存。
-  オープンメールリレー  
MTA同士のメール転送をリレーと呼び、誰からでもメール転送や送信を受け付けるMTAの設定をオープンメールリレーという。スパムメールの温床。  

```telnetを利用したSMTPサーバの動作確認について
# telnet localhost 25
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
220 localhost.localdomain ESMTP Postfix
HELO localhost
250 localhost.localdomain
MAIL FROM:root@localhost
250 2.1.0 Ok
RCPT TO:別ユーザ名@localhost
250 2.1.5 Ok
DATA
354 End data with <CR><LF>.<CR><LF>
test

.
250 2.0.0 Ok: queued as CFC8F208261E
QUIT
221 2.0.0 Bye
Connection closed by foreign host.

more /var/spool/mail/rootで確認
```

-  RBL(Realtime Blackhole List)  
迷惑メールの送信元に登録しているリスト。  
ブラックリストに載っているIPアドレスが送信元のメールについては阻止する。  
spamhausなど。