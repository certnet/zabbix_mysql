# zabbix_mysql
  Yet another mysql monitor for zabbix, like percona monitor for zabbix. only test in zabbix 2.2.

## read more:
   [percona-monitor](http://www.percona.com/doc/percona-monitoring-plugins/1.1/zabbix/index.html)

## Note
   Instead of ss_get_mysql_stats.php with mymonitor.pl, the other configuration is similar with ss_get_mysql_stats.php.

    Read more: perldoc mymonitor.pl

    mysql_port.pl: MySQL port discovery for multiport of MySQL, and generate json format strings.

    get_mysql_stats_wrapper.sh: a wrapper for parse MySQL status, runs every 5min.

## Require
```
perl-DBI
perl-DBD-mysql
```
## Install

Configure MySQL connectivity on Agent
```
1. # git clone https://github.com/chenzhe07/zabbix_mysql.git /usr/local/zabbix_mysql 

2. # bash /usr/local/zabbix_mysql/install.sh 192.168.1.2
```
### note: 192.168.1.2 is your ip address.

## Configure Zabbix Server
    
    1. import templates/zabbix_mysql_multiport.xml using Zabbix UI(Configuration -> Templates -> Import), and Create/edit hosts by assigning them “MySQL” group and linking the template “MySQL_zabbix” (Templates tab).

## Note

#### The following privileges are needed by monior user. the user and password in get_mysql_stats_wrapper.sh and mymonitor.pl can be changed, but without following privileges:
```
    PROCESS, SUPER, REPLICATION SLAVE
```
you can specify the user and password in `get_mysql_stats_wrapper.sh` with `--user` and `--password` option. 

#### As zabbix process running by zabbix user, netstat must run with following command:
```
    chmod +s /bin/netstat
```
## Test
```
# perl  mymonitor.pl --host 10.0.0.10 --port 3300 --items hv
hv:36968
# perl  mymonitor.pl --host 10.0.0.10 --port 3300 --items kx
kx:1070879944

# php ss_get_mysql_stats.php --host 10.0.0.10 --port 3300 --items hv
hv:36968
# php ss_get_mysql_stats.php --host 10.0.0.10 --port 3300 --items kx 
kx:1070911408

# zabbix_get -s 10.0.0.10 -p 10050 -k "MySQL.Bytes-received[3300]"
472339244134
```

### changelog

20170526: Add innodb status check, like [percona-monitor](https://www.percona.com/doc/percona-monitoring-plugins/LATEST/nagios/pmp-check-mysql-innodb.html)
```
item                       throttle
max_duration             if > 100s, then trigger an alarm
waiter_count             if > 10, then trigger an alarm
idle_blocker_duration    if > 200s, then trigger an alarm
```

such as:
```
zabbix_get -s cz-test2 -p 10050 -k "MySQL.max_duration[3301]"
max_duration:longest transaction active seconds: max time: 18, thread_id: 4838781, user: root@10.0.21.5:59980
```
