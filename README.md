Домашнее задание по теме 

## Задача 1

Используя docker поднимите инстанс MySQL (версию 8). Данные БД сохраните в volume.

Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/master/06-db-03-mysql/test_data) и 
восстановитесь из него.

Перейдите в управляющую консоль `mysql` внутри контейнера.

Используя команду `\h` получите список управляющих команд.

Найдите команду для выдачи статуса БД и **приведите в ответе** из ее вывода версию сервера БД.

Подключитесь к восстановленной БД и получите список таблиц из этой БД.

**Приведите в ответе** количество записей с `price` > 300.

В следующих заданиях мы будем продолжать работу с данным контейнером.

## Ответ:

Команда для выдачи статуса БД: \s (status)

mysql> \s
--------------
mysql  Ver 8.0.35 for Linux on x86_64 (MySQL Community Server - GPL)
|  Information           |  Information                          |
|------------------------|---------------------------------------|
| Connection id:         | 11                                    |
| Current database:      | test_backup                           |
| Current user:          | root@localhost                        |
| SSL:                   | Not in use                            |
| Current pager:         | stdout                                |
| sing outfile:          |''                                     |
| Using delimiter:       | ;                                     |
| Server version:        | 8.0.35 MySQL Community Server - GPL   |
| Protocol version:      | 10                                    |
| Connection:            | Localhost via UNIX socket             |
| Server characterset:   | utf8mb4                               |
| Db     characterset:   | utf8mb4                               |
| Client characterset:   | latin1                                |
| Conn.  characterset:   | latin1                                |
| UNIX socket:           | /var/run/mysqld/mysqld.sock           |
| Binary data as:        | Hexadecimal                           |
| Uptime:                | 21 min 15 sec                         |

Threads: 2  Questions: 55  Slow queries: 0  Opens: 160  Flush tables: 3  Open tables: 78  Queries per second avg: 0.043
--------------

Информацию о запросе, решил добавить в таблицу, для лучшей читаемости.

Подключение к БД: для восстановление Бд пришлось создать новую БД, команда для подключения `USE test_backup`

mysql> show tables;
|-----------------------|
| Tables_in_test_backup |
|-----------------------|
| orders                |
|-----------------------|
1 row in set (0.00 sec)

Значения > `300`

mysql> SELECT * FROM orders WHERE price > 300
    -> ;
|----|----------------|-------|
| id | title          | price |
|----+----------------|-------|
|  2 | My little pony |   500 |
|----|----------------|-------|
1 row in set (0.00 sec)

