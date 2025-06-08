<h3>zoneファイル作成</h3>

```
# pwd
/run/named

//ローカル使用ではあるものの、exampletest.localは競合しないと思って作成
//下記ゾーンファイルは/runにて作成していたため再起動で消失した。
# ls -ltr
合計 12
-rw------- 1 named named 102  6月  8 09:09 session.key
-rw-r--r-- 1 root  root  198  6月  8 10:15 exampletest.local.zone
-rw-r--r-- 1 named named   5  6月  8 10:19 named.pid

# more exampletest.local.zone
$ORIGIN exampletest.local.
$TTL 3600;
@ IN SOA ns.exampletest.local. admin.exampletest.local. (
        2010020101; //シリアル番号
        3600; //更新タイミング3600秒
        1200; //更新失敗時のリトライ間隔 秒
        1209600; //ゾーン情報の有効期間 秒（2週間）　
        900; //ネガティブキャッシュのTTL 秒
)

@               IN NS ns.exampletest.local.

ns              IN A xx.xx.xx.xx //DNSサーバのIPアドレス

/etc/named.conf
//exampletest.local
zone "exampletest.local" IN {
        type master;
        file "/var/named/exampletest.local.zone";
};

# dig @127.0.0.1 exampletest.local ANY

; <<>> DiG 9.11.4-P2-RedHat-9.11.4-26.P2.el7_9.16 <<>> @127.0.0.1 exampletest.local ANY
; (1 server found)
;; global options: +cmd
;; Got answer:
;; WARNING: .local is reserved for Multicast DNS
;; You are currently testing what happens when an mDNS query is leaked to DNS
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 43365
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;exampletest.local.             IN      ANY

;; ANSWER SECTION:
exampletest.local.      3600    IN      SOA     ns.exampletest.local. admin.exampletest.local. 2010020101 3600 1200 1209600 900
exampletest.local.      3600    IN      NS      ns.exampletest.local.

;; ADDITIONAL SECTION:
ns.exampletest.local.   3600    IN      A       xx.xx.xx.xx //DNSサーバのIPアドレス

;; Query time: 0 msec
;; SERVER: 127.0.0.1#53(127.0.0.1)
;; WHEN: 日  6月 08 10:31:40 JST 2025
;; MSG SIZE  rcvd: 121

```

<h3>エラー</h3>
おそらく/runにて作成していたゾーンファイルが再起動で消失したため、  
named起動時に下記のログが出力されたと思われる。

```
//errorログ
# systemctl status  named
● named.service - Berkeley Internet Name Domain (DNS)
   Loaded: loaded (/usr/lib/systemd/system/named.service; enabled; vendor preset: disabled)
   Active: failed (Result: exit-code) since 月 2025-06-09 07:43:47 JST; 8s ago
  Process: 1672 ExecStartPre=/bin/bash -c if [ ! "$DISABLE_ZONE_CHECKING" == "yes" ]; then /usr/sbin/named-checkconf -z "$NAMEDCONF"; else echo "Checking of zone files is disabled"; fi (code=exited, status=1/FAILURE)

 6月 09 07:43:47 localhost.localdomain bash[1672]: _default/exampletest.local/IN: file not found
 6月 09 07:43:47 localhost.localdomain bash[1672]: zone localhost.localdomain/IN: loaded serial 0
 6月 09 07:43:47 localhost.localdomain bash[1672]: zone localhost/IN: loaded serial 0
 6月 09 07:43:47 localhost.localdomain bash[1672]: zone 1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.ip6.arpa/IN: loaded serial 0
 6月 09 07:43:47 localhost.localdomain bash[1672]: zone 1.0.0.127.in-addr.arpa/IN: loaded serial 0
 6月 09 07:43:47 localhost.localdomain bash[1672]: zone 0.in-addr.arpa/IN: loaded serial 0
 6月 09 07:43:47 localhost.localdomain systemd[1]: named.service: control process exited, code=exited status=1
 6月 09 07:43:47 localhost.localdomain systemd[1]: Failed to start Berkeley Internet Name Domain (DNS).
 6月 09 07:43:47 localhost.localdomain systemd[1]: Unit named.service entered failed state.
 6月 09 07:43:47 localhost.localdomain systemd[1]: named.service failed.

```