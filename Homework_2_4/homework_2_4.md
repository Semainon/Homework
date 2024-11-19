### Д3 2.4. Создать конфигурацию syslog для отправки сообщений уровня info в файл /var/log/my.log

### Команды и шаги для настройки rsyslog

1. **`sudo nano /etc/rsyslog.conf`**: Открыть в редакторе rsyslog.conf (стандартный путь /etc/rsyslog.conf); 
2. **`*.info;mail.none;authpriv.none;cron.none /var/log/my.log`**: В конце файла добавить правило, указывающее что все сообщения уровня info (и выше), кроме сообщений от почтовых систем, аутентификации и планировщика задач, будут записываться в файл /var/log/my.log;
3. **`Убедиться, что файл /var/log/my.log существует и имеет правильные разрешения, если нет – создать его:`** 
4. **`sudo touch /var/log/my.log`**
5. **`sudo chmod 644 /var/log/my.log`** 
6. **`sudo systemctl restart rsyslog`**: Перезапустить rsyslog, чтобы изменения вступили в силу;
7. **`logger -p user.info "Тестовое сообщение уровня info"`**: Посредством logger отправляем тестовое сообщение уровня info;
7. **`cat /var/log/my.log"`**: Удостоверились, что сообщение отобразилось в файле my.log;
 
```bash
root@Zero etc]# sudo nano /etc/rsyslog.conf
[root@Zero etc]# cd /var/log && ls
anaconda           btmp                   cron             dnf.rpm.log          maillog            private  secure-20241117   tuned
audit              chrony                 cron-20241117    hawkey.log           maillog-20241117   qemu-ga  spooler           wtmp
boot.log           cloud-init-output.log  dnf.librepo.log  hawkey.log-20241117  messages           samba    spooler-20241117
boot.log-20241116  cloud-init.log         dnf.log          lastlog              messages-20241117  secure   sssd
[root@Zero log]# sudo touch /var/log/my.log
[root@Zero log]# sudo chmod 644 /var/log/my.log
[root@Zero log]# sudo systemctl restart rsyslog
[root@Zero log]# logger -p user.info "Тестовое сообщение уровня info"
[root@Zero log]# cat /var/log/my.log
Nov 19 16:54:59 Zero systemd[1]: Stopping System Logging Service...
Nov 19 16:55:00 Zero rsyslogd[1038]: [origin software="rsyslogd" swVersion="8.2102.0-7.el8_6.1" x-pid="1038" x-info="https://www.rsyslog.com"] exiting on signal 15.
Nov 19 16:55:00 Zero systemd[1]: rsyslog.service: Succeeded.
Nov 19 16:55:00 Zero systemd[1]: Stopped System Logging Service.
Nov 19 16:55:00 Zero systemd[1]: Starting System Logging Service...
Nov 19 16:55:00 Zero rsyslogd[15944]: [origin software="rsyslogd" swVersion="8.2102.0-7.el8_6.1" x-pid="15944" x-info="https://www.rsyslog.com"] start
Nov 19 16:55:00 Zero systemd[1]: Started System Logging Service.
Nov 19 16:55:00 Zero rsyslogd[15944]: imjournal: journal files changed, reloading...  [v8.2102.0-7.el8_6.1 try https://www.rsyslog.com/e/0 ]
Nov 19 16:55:43 Zero root[15950]: Тестовое сообщение уровня info
```