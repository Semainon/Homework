### Д3 2.2. При помощи logger записать в общий журнал послание добра и мира посланникам из Альфа-Центавра с facility kernel

### Утилита Logger и команды 

1. **`logger`**: Утилита в Unix-подобных ОС, которая предоставляет командный интерфейс для записи сообщений в системный журнал. Позволяет пользователям и приложениям отправлять сообщения в журнал, который может быть использован для мониторинга и отладки системы. С помощью logger можно записывать сообщения с различными уровнями важности и метками.
2. **`facility`**: В контексте утилиты logger и системного журнала — это категория или источник сообщений, которые записываются в журнал, например, сообщения от ядра, системных служб, приложений и т.д. Это позволяет различать сообщения, поступающие из разных источников. Примеры: 
	- kern: сообщения от ядра операционной системы.
	- user: сообщения от пользовательских приложений.
	- mail: сообщения, связанные с почтовыми системами.
	- daemon: сообщения от системных демонов.
	- auth: сообщения, связанные с безопасностью и аутентификацией
3. **`logger -p kern.info -t kernel "Текст сообщения"`**: Команда записывает информационное сообщение в системный журнал, указывая, что оно связано с ядром (-p kern.info), и помечает его тегом kernel (-t kernel).
 
```bash
[[root@Zero ~]# logger -p kern.info -t kernel "Добро и мир посланникам из Альфа-Центавра"
[root@Zero ~]# journalctl | tail
Nov 19 15:01:01 Zero.local CROND[15810]: (root) CMD (run-parts /etc/cron.hourly)
Nov 19 15:01:01 Zero.local run-parts[15813]: (/etc/cron.hourly) starting 0anacron
Nov 19 15:01:01 Zero.local run-parts[15819]: (/etc/cron.hourly) finished 0anacron
Nov 19 15:04:26 Zero.local kernel[15821]: Привет от kernel
Nov 19 15:08:35 Zero.local sshd[15828]: Invalid user ubuntu from 45.148.10.240 port 54556
Nov 19 15:08:35 Zero.local sshd[15828]: Connection closed by invalid user ubuntu 45.148.10.240 port 54556 [preauth]
Nov 19 15:12:00 Zero.local root[15832]: Тест
Nov 19 15:15:50 Zero.local sshd[15837]: Invalid user ubuntu from 45.148.10.240 port 49490
Nov 19 15:15:50 Zero.local sshd[15837]: Connection closed by invalid user ubuntu 45.148.10.240 port 49490 [preauth]
Nov 19 15:16:25 Zero.local kernel[15840]: Добро и мир посланникам из Альфа-Центавра
```