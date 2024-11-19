### Д3 2.5. Все события ssh записывать параллельно в отдельный файл, производить ротацию каждые сутки или по размеру (не более 1 мегабайта), всего 10 файлов в ротации

### Настройка логирования SSH с ротацией

1. **`sudo nano /etc/ssh/sshd_config`**: Открыть в редакторе конфигурационный файл SSH; 
2. Добавить или изменить строки для настройки уровня логирования:
```bash
	SyslogFacility AUTH
	LogLevel INFO 
```
	**`Примечание:`**:
	- Уровень логирования INFO записывает важные события, такие как успешные подключения и отключения, но не включает более детальные сообщения, если нужно более подробно ставим уровень VERBOSE (LogLevel VERBOSE);
3. **`sudo nano /etc/rsyslog.d/ssh.conf`**: Открыть и отредактировать отдельный файл для конфигурации SSH:
```bash
if $programname == 'sshd' then /var/log/ssh.log
& stop
```
	**`Примечание:`**:
	- Эта строка указывает, что все сообщения от программы sshd должны записываться в файл /var/log/ssh.log, а & stop предотвращает дальнейшую обработку этих сообщений.
	- Дополнительно следует удостовериться, что путь к файлу /etc/rsyslog.d/ssh.conf указан в основном конфигурационном файле rsyslog.conf

4. **`sudo nano /etc/logrotate.d/ssh`**: Создать файл конфигурации для logrotate и добавить конфигурацию; 
```bash
/var/log/ssh.log {
    daily
    rotate 10
    size 1M
    compress
    missingok
    notifempty
    create 640 root adm
    sharedscripts
    postrotate
        /usr/bin/systemctl reload sshd > /dev/null 2>&1 || true
    endscript
}
```
	**`Объяснение конфигурации logrotate`**:
- /var/log/auth.log: Это файл, в который будут записываться события SSH. 
- daily: Ротация логов будет происходить каждый день.
- rotate 10: Хранить 10 старых файлов логов перед их удалением.
- size 1M: Ротация будет происходить, если файл превысит 1 мегабайт.
- compress: Сжимать старые файлы логов для экономии места.
- missingok: Не выдавать ошибку, если файл лога отсутствует.
- notifempty: Не ротировать файл, если он пустой.
- create 640 root adm: Создавать новый файл лога с указанными правами после ротации.
- sharedscripts: Выполнять скрипт postrotate только один раз для всех логов, которые ротируются.
- postrotate: Перезагружать службу SSH после ротации, чтобы она продолжала корректно записывать логи.
5. **`sudo sshd -t`**: Проверка конфигурацию на наличие ошибок, если ошибок нет, никаких сообщений не будет; 
6. **`sudo systemctl restart sshd`**: Перезапустить службу SSH, чтобы изменения вступили в силу; 
7. **`Файл логов /var/log/ssh.lod`**: Если файл /var/log/ssh.log еще не существует, создайть его и установить нужные доступа; 
```bash
sudo touch /var/log/ssh.log
sudo chown root:adm /var/log/ssh.log
sudo chmod 640 /var/log/ssh.log
```
8. **`sudo systemctl restart rsyslog`**: Перезапустить службу rsyslog, чтобы изменения вступили в силу; 
9. **`sudo tail -f /var/log/ssh.log`**: Проверка, записываютсяли логи SSH в файл /var/log/ssh.log. Выполнить вход по SSH и затем просмотреть файл;

```bash
[root@Zero log]# sudo nano /etc/ssh/sshd_config
[root@Zero log]# sudo nano /etc/logrotate.d/ssh
[root@Zero log]# sudo systemctl restart sshd
[root@Zero log]# sudo touch /var/log/ssh.log
[root@Zero log]# sudo chown root:adm /var/log/ssh.log
[root@Zero log]# sudo chmod 640 /var/log/ssh.log
[root@Zero log]# sudo systemctl restart rsyslog
[root@Zero log]# sudo tail -f /var/log/ssh.log
Nov 19 22:21:05 Zero sshd[17720]: Connection closed by authenticating user root 46.19.143.66 port 59388 [preauth]
Nov 19 22:21:05 Zero sshd[17723]: Invalid user admin from 46.19.143.66 port 59400
Nov 19 22:21:06 Zero sshd[17723]: Connection closed by invalid user admin 46.19.143.66 port 59400 [preauth]
Nov 19 22:21:14 Zero sshd[17726]: Invalid user baikal from 46.19.143.66 port 59408
Nov 19 22:21:14 Zero sshd[17726]: Connection closed by invalid user baikal 46.19.143.66 port 59408 [preauth]
Nov 19 22:21:20 Zero sshd[17729]: error: kex_exchange_identification: Connection closed by remote host
Nov 19 22:21:48 Zero sshd[17731]: Received disconnect from 167.71.32.208 port 46164:11: Bye Bye [preauth]
Nov 19 22:21:48 Zero sshd[17731]: Disconnected from 167.71.32.208 port 46164 [preauth]
Nov 19 22:22:37 Zero sshd[17735]: Accepted publickey for root from 88.201.181.66 port 56385 ssh2: RSA SHA256:dFeG+pNnJlStaUuNPWAsVf1PNscMpq1/ThjpmzLqRoQ
Nov 19 22:22:37 Zero sshd[17735]: pam_unix(sshd:session): session opened for user root by (uid=0)
```

### Проверка настройки логирования SSH с ротацией

1. **`sls -lh /var/log/ssh.log*`**: Команда покажет все файлы, начинающиеся с ssh.log, включая текущий файл и все ротированные версии; 
2. **`Проверка ротированных файлов`**: После выполнения ротации логов должно быть несколько файлов, например:
	- ssh.log — текущий файл логов
	- ssh.log.1 — последний ротированный файл
	- ssh.log.1.gz — сжатый файл, если сжатие настроено
3. **`zcat /var/log/ssh.log.1.gz | less*`**: Команда выводитсодержимое сжатого файла без его распаковки.

```bash
[root@Zero log]# sudo logrotate -f /etc/logrotate.d/ssh
[root@Zero log]# ls -lh /var/log/ssh.log*
-rw-r-----. 1 root adm    0 Nov 19 22:36 /var/log/ssh.log
-rw-r-----. 1 root adm 1.8K Nov 19 22:26 /var/log/ssh.log.1.gz
[root@Zero log]# zcat /var/log/ssh.log.1.gz | less

Nov 19 18:39:30 Zero sshd[17390]: error: kex_exchange_identification: Connection closed by remote host
Nov 19 18:40:44 Zero sshd[17392]: Received disconnect from 167.71.32.208 port 59366:11: Bye Bye [preauth]
Nov 19 18:40:44 Zero sshd[17392]: Disconnected from 167.71.32.208 port 59366 [preauth]
Nov 19 18:41:38 Zero sshd[17398]: error: kex_exchange_identification: Connection closed by remote host
Nov 19 18:50:14 Zero sshd[17439]: error: kex_exchange_identification: banner line contains invalid characters
Nov 19 18:53:29 Zero sshd[17446]: Connection closed by authenticating user root 139.19.117.129 port 32852 [preauth]
Nov 19 19:02:18 Zero sshd[17466]: Invalid user app from 161.35.28.55 port 38310
Nov 19 19:02:18 Zero sshd[17466]: Connection closed by invalid user app 161.35.28.55 port 38310 [preauth]
Nov 19 19:05:43 Zero sshd[17473]: Received disconnect from 167.71.32.208 port 57916:11: Bye Bye [preauth]
Nov 19 19:05:43 Zero sshd[17473]: Disconnected from 167.71.32.208 port 57916 [preauth]
Nov 19 19:09:47 Zero sshd[17479]: Invalid user appuser from 161.35.28.55 port 34102
Nov 19 19:09:47 Zero sshd[17479]: Connection closed by invalid user appuser 161.35.28.55 port 34102 [preauth]
Nov 19 19:17:16 Zero sshd[17483]: Invalid user bigdata from 161.35.28.55 port 54990
Nov 19 19:17:16 Zero sshd[17483]: Connection closed by invalid user bigdata 161.35.28.55 port 54990 [preauth]
Nov 19 19:30:48 Zero sshd[17488]: Received disconnect from 167.71.32.208 port 56486:11: Bye Bye [preauth]
Nov 19 19:30:48 Zero sshd[17488]: Disconnected from 167.71.32.208 port 56486 [preauth]
Nov 19 19:32:13 Zero sshd[17491]: Invalid user centos from 161.35.28.55 port 57962
Nov 19 19:32:13 Zero sshd[17491]: Connection closed by invalid user centos 161.35.28.55 port 57962 [preauth]
Nov 19 19:39:42 Zero sshd[17495]: Invalid user centos from 161.35.28.55 port 40766
Nov 19 19:39:42 Zero sshd[17495]: Connection closed by invalid user centos 161.35.28.55 port 40766 [preauth]
Nov 19 19:47:10 Zero sshd[17499]: Invalid user data from 161.35.28.55 port 51604
Nov 19 19:47:10 Zero sshd[17499]: Connection closed by invalid user data 161.35.28.55 port 51604 [preauth]
Nov 19 19:50:27 Zero sshd[17502]: error: kex_exchange_identification: Connection closed by remote host
Nov 19 19:54:39 Zero sshd[17505]: Invalid user demo from 161.35.28.55 port 37284
Nov 19 19:54:39 Zero sshd[17505]: Connection closed by invalid user demo 161.35.28.55 port 37284 [preauth]
Nov 19 19:55:54 Zero sshd[17508]: Received disconnect from 167.71.32.208 port 54970:11: Bye Bye [preauth]
Nov 19 19:55:54 Zero sshd[17508]: Disconnected from 167.71.32.208 port 54970 [preauth]
Nov 19 20:05:13 Zero sshd[17524]: Connection reset by 37.46.122.88 port 5126 [preauth]
Nov 19 20:05:25 Zero sshd[17527]: Connection reset by 37.46.122.88 port 2512 [preauth]
Nov 19 20:20:49 Zero sshd[17531]: Received disconnect from 167.71.32.208 port 53476:11: Bye Bye [preauth]
Nov 19 20:20:49 Zero sshd[17531]: Disconnected from 167.71.32.208 port 53476 [preauth]
Nov 19 20:24:33 Zero sshd[17534]: Invalid user docker from 161.35.28.55 port 56490
Nov 19 20:24:34 Zero sshd[17534]: Connection closed by invalid user docker 161.35.28.55 port 56490 [preauth]
Nov 19 20:32:02 Zero sshd[17537]: Invalid user elastic from 161.35.28.55 port 40636
Nov 19 20:32:02 Zero sshd[17537]: Connection closed by invalid user elastic 161.35.28.55 port 40636 [preauth]
Nov 19 20:45:06 Zero sshd[17549]: Received disconnect from 167.71.32.208 port 52014:11: Bye Bye [preauth]
Nov 19 20:45:06 Zero sshd[17549]: Disconnected from 167.71.32.208 port 52014 [preauth]
Nov 19 20:55:46 Zero sshd[17552]: Connection closed by authenticating user root 139.19.117.129 port 55354 [preauth]
Nov 19 20:56:22 Zero sshd[17555]: Connection closed by 66.240.236.116 port 48040 [preauth]
Nov 19 21:01:53 Zero sshd[17570]: Invalid user elsearch from 161.35.28.55 port 44870
Nov 19 21:01:53 Zero sshd[17570]: Connection closed by invalid user elsearch 161.35.28.55 port 44870 [preauth]
Nov 19 21:09:40 Zero sshd[17574]: Received disconnect from 167.71.32.208 port 50520:11: Bye Bye [preauth]
Nov 19 21:09:40 Zero sshd[17574]: Disconnected from 167.71.32.208 port 50520 [preauth]
:
```