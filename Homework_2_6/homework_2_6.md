### Д3 2.6. Вывести сообщения с последнего запуска системы. Вывести сообщения не старше одного часа.

### Команды

1. **`journalctl -b`**: Команда покажет все сообщения, начиная с последнего запуска системы; 
2. **`journalctl --since "1 hour ago"`**: а команда выведет все сообщения, которые были записаны в журнале за последний час. 
```bash
Last login: Tue Nov 19 22:22:37 2024 from 88.201.181.66
[root@Zero ~]# journalctl -b --since "1 hour ago" > output_1.log
[root@Zero ~]# journalctl -b > output_2.log
[root@Zero ~]# ls
output_1.log  output_2.log
```
