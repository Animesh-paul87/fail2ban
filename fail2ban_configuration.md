# fail2ban
fail2ban configuration

Required repository EPEL

**```  $ sudo dnf install epel-release -y  ```**

Install Fail2ban
``` $ sudo dnf install fail2ban -y ```

enable and staart the service
$ sudo systemctl enable fail2ban --now

go to the configuration Directory
$ cd /etc/fail2ban

create a backup of the mail configuration file.
$ mv fail2ban.conf fail2ban.conf_bak

then create a copy of jail.conf
$ cp jail.conf jail.local

open jail.local with nano/vi
uncoment the ignoreip field line 92 and add trusted ip, which will not be observed by fail2ban, here the ip is 192.168.77.147
ignoreip = 127.0.0.1/8 ::1 192.168.77.147

restart the fail2ban service
$ sudo systemctl restart fail2ban

then go inside jail.d directory  
$ cd /etc/fail2ban/jail.d/

Create a new file name sshd.local
$ nano sshd.local

and add below lines to it.
```
[sshd]
enabled = true
port = ssh
action = iptables-multiport
logpath = /var/log/secure  # Log file location
maxretry = 3      # Max password attempt
bantime = 600     # Ban the ip for next 10 minutes
```
save the file and restart the fail2ban service.

try to connect with any other ip than the ignored one


below are the log file which is showing the blocking

```
2026-11-01 07:10:30,709 fail2ban.server         [2408]: INFO    Starting Fail2ban v1.1.0
2026-11-01 07:10:30,710 fail2ban.observer       [2408]: INFO    Observer start...
2026-11-01 07:10:30,715 fail2ban.database       [2408]: INFO    Connected to fail2ban persistent database '/var/lib/fail2ban/fail2ban.sqlite3'
2026-11-01 07:10:30,716 fail2ban.jail           [2408]: INFO    Creating new jail 'sshd'
2026-11-01 07:10:30,726 fail2ban.jail           [2408]: INFO    Jail 'sshd' uses systemd {}
2026-11-01 07:10:30,726 fail2ban.jail           [2408]: INFO    Initiated 'systemd' backend
2026-11-01 07:10:30,727 fail2ban.filter         [2408]: INFO      maxLines: 1
2026-11-01 07:10:30,744 fail2ban.filtersystemd  [2408]: INFO    [sshd] Added journal match for: '_SYSTEMD_UNIT=sshd.service + _COMM=sshd + _COMM=sshd-session'
2026-11-01 07:10:30,744 fail2ban.filter         [2408]: INFO      maxRetry: 3
2026-11-01 07:10:30,744 fail2ban.filter         [2408]: INFO      findtime: 600
2026-11-01 07:10:30,745 fail2ban.actions        [2408]: INFO      banTime: 600
2026-11-01 07:10:30,745 fail2ban.filter         [2408]: INFO      encoding: UTF-8
2026-11-01 07:10:30,746 fail2ban.filtersystemd  [2408]: INFO    [sshd] Jail is in operation now (process new journal entries)
2026-11-01 07:10:30,746 fail2ban.jail           [2408]: INFO    Jail 'sshd' started
2026-11-01 07:13:46,903 fail2ban.filter         [2408]: INFO    [sshd] Found 10.10.10.122 - 2026-11-01 07:13:45
2026-11-01 07:13:50,974 fail2ban.filter         [2408]: INFO    [sshd] Found 10.10.10.122 - 2026-11-01 07:13:50
2026-11-01 07:13:56,313 fail2ban.filter         [2408]: INFO    [sshd] Found 10.10.10.122 - 2026-11-01 07:13:55
2026-11-01 07:13:56,973 fail2ban.actions        [2408]: NOTICE  [sshd] Ban 10.10.10.122
2026-11-01 07:15:20,808 fail2ban.actions        [2408]: NOTICE  [sshd] Unban 10.10.10.122
2026-11-01 07:15:35,224 fail2ban.filter         [2408]: INFO    [sshd] Found 10.10.10.122 - 2026-11-01 07:15:34
2026-11-01 07:15:41,724 fail2ban.filter         [2408]: INFO    [sshd] Found 10.10.10.122 - 2026-11-01 07:15:41
2026-11-01 07:15:46,474 fail2ban.filter         [2408]: INFO    [sshd] Found 10.10.10.122 - 2026-11-01 07:15:46
2026-11-01 07:15:47,205 fail2ban.actions        [2408]: NOTICE  [sshd] Ban 10.10.10.122
2026-11-01 07:25:46,476 fail2ban.actions        [2408]: NOTICE  [sshd] Unban 10.10.10.122
```


Unblocking a block listed ip for the emergency case.

$ sudo fail2ban-client set sshd unbanip 192.168.1.69





