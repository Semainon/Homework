### Д3 2.6. Вывести сообщения с последнего запуска системы. Вывести сообщения не старше одного часа.

### Команды

1. **`journalctl -b`**: Команда покажет все сообщения, начиная с последнего запуска системы; 
2. **`journalctl --since "1 hour ago"`**: а команда выведет все сообщения, которые были записаны в журнале за последний час. 
```bash
Last login: Tue Nov 19 22:22:37 2024 from **.***.***.**
[root@Zero ~]# journalctl -b --since "1 hour ago" > output_1.log
[root@Zero ~]# journalctl -b > output_2.log
[root@Zero ~]# ls
output_1.log  output_2.log
[root@Zero ~]# rm output_1.log output_2.log
```
3. **`sftp user@server"`**: Поключаемся к серверу по протоколу SFTP, который работает поверх SSH. Он обеспечивает шифрование всех передаваемых данных, включая файлы, имена пользователей и пароли, что делает его значительно более безопасным по сравнению с FTP
4. **`get /path/to/remote/file /path/to/local/directory`**: Скачиваем файлы командой get в нужную директорию 

```bash
Trickster@MBP-Zero ~ % sftp root@**.***.***.**
Connected to **.***.***.**.
sftp> get /root/output_1.log /Users/Trickster/Desktop/Homework
Fetching /root/output_1.log to /Users/Trickster/Desktop/Homework/output_1.log
output_1.log                                  100%   19KB 452.8KB/s   00:00    
sftp> get /root/output_2.log /Users/Trickster/Desktop/Homework                [ Fetching /root/output_2.log to /Users/Trickster/Desktop/Homework/output_2.log
output_2.log                                  100% 1114KB   3.9MB/s   00:00  
```