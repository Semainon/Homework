### Д3 2.3 Посмотреть последние 25 строчек лога сервиса systemd-logind

### Команды 

1. **`journalctl -u systemd-logind.service -n 25`**: Команда отображающая последние 25 строк лога для сервиса systemd-logind: 
	- journalctl: утилита для доступа к журналам, собранным systemd-journald;
	- -u systemd-logind.service: опция указывает, что нужно просмотреть записи, относящиеся к конкретному сервису, в данном случае systemd-logind;
	- -n 25: Эта опция указывает, что нужно вывести последние 25 записей из журнала;
2. **`journalctl -u systemd-logind.service | wc -l`**: Фильтрация по юниту и подсчет записей. 
3. **`Примечание:`** Поскольку в данном слуяае записей меньше 25, команда выведет имеющиеся. 
 
```bash
[root@Zero ~]# journalctl -u systemd-logind.service -n 25
-- Logs begin at Sat 2024-11-16 09:42:40 UTC, end at Tue 2024-11-19 16:06:35 UTC. --
Nov 16 09:42:45 Zero.local systemd[1]: Starting Login Service...
Nov 16 09:42:45 Zero.local systemd-logind[671]: New seat seat0.
Nov 16 09:42:45 Zero.local systemd-logind[671]: Watching system buttons on /dev/input/event0 (Power Button)
Nov 16 09:42:45 Zero.local systemd-logind[671]: Watching system buttons on /dev/input/event1 (AT Translated Set 2 keyboard)
Nov 16 09:42:45 Zero.local systemd[1]: Started Login Service.
Nov 16 09:44:11 Zero.local systemd-logind[671]: New session 1 of user root.
Nov 16 09:44:31 Zero.local systemd-logind[671]: Session 1 logged out. Waiting for processes to exit.
Nov 16 09:44:31 Zero.local systemd-logind[671]: Removed session 1.
Nov 19 10:35:09 Zero.local systemd-logind[671]: New session 3 of user root.
Nov 19 11:46:24 Zero.local systemd-logind[671]: Session 3 logged out. Waiting for processes to exit.
Nov 19 11:46:24 Zero.local systemd-logind[671]: Removed session 3.
Nov 19 11:46:28 Zero.local systemd-logind[671]: New session 5 of user root.
Nov 19 11:46:46 Zero.local systemd-logind[671]: Session 5 logged out. Waiting for processes to exit.
Nov 19 11:46:46 Zero.local systemd-logind[671]: Removed session 5.
Nov 19 11:48:37 Zero.local systemd-logind[671]: New session 6 of user root.
Nov 19 11:52:45 Zero.local systemd-logind[671]: Session 6 logged out. Waiting for processes to exit.
Nov 19 11:52:45 Zero.local systemd-logind[671]: Removed session 6.
Nov 19 11:52:47 Zero.local systemd-logind[671]: New session 8 of user root.
[root@Zero ~]# journalctl -u systemd-logind.service | wc -l
19
```