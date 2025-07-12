```samba.conf
# more /etc/samba/smb.conf
# See smb.conf.example for a more detailed config file or
# read the smb.conf manpage.
# Run 'testparm' to verify the config is correct after
# you modified it.

[global]
        workgroup = SAMBA
        security = user

        passdb backend = tdbsam

        printing = cups
        printcap name = cups
        load printers = yes
        cups options = raw

[homes]
        comment = Home Directories
        valid users = %S, %D%w%S
        browseable = No
        read only = No
        inherit acls = Yes

[printers]
        comment = All Printers
        path = /var/tmp
        printable = Yes
        create mask = 0600
        browseable = No

[print$]
        comment = Printer Drivers
        path = /var/lib/samba/drivers
        write list = @printadmin root
        force group = @printadmin
        create mask = 0664
        directory mask = 0775
```

```
# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: eth0
  sources:
  services: dhcpv6-client dns http samba
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

# systemctl start smb
# systemctl status smb
● smb.service - Samba SMB Daemon
   Loaded: loaded (/usr/lib/systemd/system/smb.service; disabled; vendor preset: disabled)
   Active: active (running) since 土 2025-06-21 08:40:58 JST; 6s ago
     Docs: man:smbd(8)
           man:samba(7)
           man:smb.conf(5)
 Main PID: 1694 (smbd)
   Status: "smbd: ready to serve connections..."
   CGroup: /system.slice/smb.service
           tq1694 /usr/sbin/smbd --foreground --no-process-group
           tq1696 /usr/sbin/smbd --foreground --no-process-group
           tq1697 /usr/sbin/smbd --foreground --no-process-group
           mq1698 /usr/sbin/smbd --foreground --no-process-group

 6月 21 08:40:55 localhost.localdomain systemd[1]: Starting Samba SMB Daemon...
 6月 21 08:40:58 localhost.localdomain smbd[1694]: [2025/06/21 08:40:58.257355,  0] ../...y)
 6月 21 08:40:58 localhost.localdomain smbd[1694]:   daemon_ready: daemon 'smbd' finish...ns
 6月 21 08:40:58 localhost.localdomain systemd[1]: Started Samba SMB Daemon.
Hint: Some lines were ellipsized, use -l to show in full.
```

```  useradd
#pdbedit -a testCentOS
new password:
retype new password:
Unix username:        testCentOS
NT username:
Account Flags:        [U          ]
User SID:             S-1-5-21-271714995-2932044201-234104226-1001
Primary Group SID:    S-1-5-21-271714995-2932044201-234104226-513
Full Name:
Home Directory:       \\centos7\testcentos
HomeDir Drive:
Logon Script:
Profile Path:         \\centos7\testcentos\profile
Domain:               CENTOS7
Account desc:
Workstations:
Munged dial:
Logon time:           0
Logoff time:          木, 07  2月 2036 00:06:39 JST
Kickoff time:         木, 07  2月 2036 00:06:39 JST
Password last set:    土, 12  7月 2025 10:08:25 JST
Password can change:  土, 12  7月 2025 10:08:25 JST
Password must change: never
Last bad password   : 0
Bad password count  : 0
Logon hours         : FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF

# vim /etc/samba/smb.conf
[homes]
        comment = Home Directories
        valid users = otheruser, testCentOS

Windows側のエクスプローラーで下記を実行する。
\\IPアドレス\testCentOS
資格情報を入力してログイン。

windows側でsambaログインユーザ変更をしたい場合は、
コマンドプロンプトでnet use
接続情報が出てくるので、net use \\IPアドレス\testCentOS /delete
で消去。

＃ smbstatus

Samba version 4.10.16
PID     Username     Group        Machine                                   Protocol Version  Encryption           Signing
----------------------------------------------------------------------------------------------------------------------------------------
1919    testCentOS   testCentOS   IPアドレス (ipv4:IPアドレス:56967)    SMB3_11           -                    partial(AES-128-CMAC)

Service      pid     Machine       Connected at                     Encryption   Signing
---------------------------------------------------------------------------------------------
testCentOS   1919    IPアドレス  土  7月 12 10時28分34秒 2025 JST -            -
他のユーザ      1919    IPアドレス  土  7月 12 10時28分30秒 2025 JST -            -

Locked files:
Pid          User(ID)   DenyMode   Access      R/W        Oplock           SharePath   Name   Time
--------------------------------------------------------------------------------------------------
1919         1001       DENY_NONE  0x100081    RDONLY     NONE             /home/testCentOS   .   Sat Jul 12 10:28:33 2025
1919         1001       DENY_NONE  0x100081    RDONLY     NONE             /home/testCentOS   .   Sat Jul 12 10:28:33 2025
1919         1001       DENY_NONE  0x100081    RDONLY     NONE             /home/testCentOS   .   Sat Jul 12 10:28:33 2025


```

