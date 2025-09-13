```
# pwd
/etc/fail2ban/jail.d
# more custom.local
[sshd]
enabled = true
クライアントからのSSH接続PW失敗後
# fail2ban-client status sshd
Status for the jail: sshd
|- Filter
|  |- Currently failed: 1
|  |- Total failed:     11
|  `- Journal matches:  _SYSTEMD_UNIT=sshd.service + _COMM=sshd
`- Actions
   |- Currently banned: 1
   |- Total banned:     1
   `- Banned IP list:   192.168.3.40（クライアントIP）

# more /var/log/fail2ban.log
2025-09-13 10:05:14,251 fail2ban.jail           [3351]: INFO    Jail 'sshd' star
ted
2025-09-13 10:05:14,251 fail2ban.filter         [3351]: WARNING [sshd] Please ch
eck jail has possibly a timezone issue. Line with odd timestamp: ('', '2025-09-1
3T10:02:40.472109', 'localhost.localdomain sshd[3331]: Failed password for ユーザ名 from 192.168.3.40 port 40394 ssh2')
2025-09-13 10:05:14,252 fail2ban.filter         [3351]: INFO    [sshd] Found 192
.168.3.40 - 2025-09-13 10:05:14
2025-09-13 10:05:14,252 fail2ban.filter         [3351]: INFO    [sshd] Found 192
.168.3.40 - 2025-09-13 10:05:14
2025-09-13 10:05:14,253 fail2ban.filter         [3351]: INFO    [sshd] Found 192
.168.3.40 - 2025-09-13 10:05:14
2025-09-13 10:05:14,253 fail2ban.filter         [3351]: INFO    [sshd] Found 192
.168.3.40 - 2025-09-13 10:05:14
2025-09-13 10:05:14,254 fail2ban.filter         [3351]: INFO    [sshd] Found 192
.168.3.40 - 2025-09-13 10:05:14
2025-09-13 10:05:14,254 fail2ban.filter         [3351]: INFO    [sshd] Found 192
.168.3.40 - 2025-09-13 10:05:14
2025-09-13 10:05:14,450 fail2ban.actions        [3351]: NOTICE  [sshd] Ban 192.1
68.3.40
2025-09-13 10:05:34,202 fail2ban.filter         [3351]: INFO    [sshd] Found 192
.168.3.40 - 2025-09-13 10:05:34
2025-09-13 10:05:34,414 fail2ban.filter         [3351]: INFO    [sshd] Found 192
.168.3.40 - 2025-09-13 10:05:34
2025-09-13 10:05:39,983 fail2ban.filter         [3351]: INFO    [sshd] Found 192
.168.3.40 - 2025-09-13 10:05:39
2025-09-13 10:05:41,214 fail2ban.filter         [3351]: INFO    [sshd] Found 192
.168.3.40 - 2025-09-13 10:05:41
2025-09-13 10:05:41,397 fail2ban.actions        [3351]: NOTICE  [sshd] 192.168.3
.40 already banned
2025-09-13 10:05:41,492 fail2ban.filter         [3351]: INFO    [sshd] Found 192
.168.3.40 - 2025-09-13 10:05:41

```

