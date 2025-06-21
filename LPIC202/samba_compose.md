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

