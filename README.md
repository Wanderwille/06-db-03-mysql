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


Информацию о запросе, решил добавить в таблицу, для лучшей читаемости.

Подключение к БД: для восстановление Бд пришлось создать новую БД, команда для подключения `USE test_backup`

mysql> show tables;
|-----------------------|
| Tables_in_test_backup |
|-----------------------|
| orders                |
|-----------------------|
1 row in set (0.00 sec)

### Значения > `300`

mysql> SELECT * FROM orders WHERE price > 300;

| id | title          | price |
|----|----------------|-------|
|  2 | My little pony |   500 |
|----|----------------|-------|

1 row in set (0.00 sec)

## Задача 2

Создайте пользователя test в БД c паролем test-pass, используя:

- плагин авторизации mysql_native_password
- срок истечения пароля - 180 дней
- количество попыток авторизации - 3
- максимальное количество запросов в час - 100
- аттрибуты пользователя:
    - Фамилия "Pretty" 
    - Имя "James"
- Предоставьте привелегии пользователю test на операции SELECT базы test_db.

Используя таблицу INFORMATION_SCHEMA.USER_ATTRIBUTES получите данные по пользователю test и приведите в ответе к задаче.


## Ответ:

CREATE USER 'test'@'%' IDENTIFIED WITH mysql_native_password BY '12345'

PASSWORD EXPIRE INTERVAL 180 DAY

FAILED_LOGIN_ATTEMPTS 3

MAX_QUERIES_PER_HOUR 100

ATTRIBUTE '{"fname": "James" , "lname": "Pretty" }'; 

GRANT SELECT ON test_backup.* TO 'test';

SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES WHERE USER = 'test';

```
+------+------+---------------------------------------+
| USER | HOST | ATTRIBUTE                             |
+------+------+---------------------------------------+
| test | %    | {"fname": "James", "lname": "Pretty"} |
+------+------+---------------------------------------+
1 row in set (0.00 sec)
```

## Задача 3

Установите профилирование `SET profiling = 1`.
Изучите вывод профилирования команд `SHOW PROFILES;`.

Исследуйте, какой `engine` используется в таблице БД `test_db` и **приведите в ответе**.

Измените `engine` и **приведите время выполнения и запрос на изменения из профайлера в ответе**:
- на `MyISAM`
- на `InnoDB`

## Ответ:

SHOW CREATE TABLE orders;

```
 Table   Create Table               
 orders  CREATE TABLE `orders` (
  `id` int unsigned NOT NULL AUTO_INCREMENT,
  `title` varchar(80) NOT NULL,
  `price` int DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci |
```

```
ALTER TABLE orders ENGINE=MyISAM;  
show profiles;  
+----------+------------+----------------------------------+
| Query_ID | Duration   | Query                            |
+----------+------------+----------------------------------+
|       36 | 0.01115211 | ALTER TABLE orders ENGINE=MyISAM |
+----------+------------+----------------------------------+
```
- на `InnoDB`:
```
ALTER TABLE orders ENGINE=InnoDB;   
show profiles;  
+----------+------------+----------------------------------+
| Query_ID | Duration   | Query                            |
+----------+------------+----------------------------------+
|       37 | 0.02306510 | ALTER TABLE orders ENGINE=InnoDB |
+----------+------------+----------------------------------+
```

## Задача 4 

Изучите файл `my.cnf` в директории /etc/mysql.

Измените его согласно ТЗ (движок InnoDB):
- Скорость IO важнее сохранности данных
- Нужна компрессия таблиц для экономии места на диске
- Размер буффера с незакомиченными транзакциями 1 Мб
- Буффер кеширования 30% от ОЗУ
- Размер файла логов операций 100 Мб

Приведите в ответе измененный файл `my.cnf`.


Скорость IO важнее сохранности данных:  
`innodb_flush_method = O_DSYNC`  
`innodb_flush_log_at_trx_commit = 0`  

Нужна компрессия таблиц для экономии места на диске:  
`innodb_file_per_table = 1`

Размер буфера с незакомиченными транзакциями 1 Мб:  
`innodb_log_buffer_size = 1M`  

Буфер кеширования 30% от ОЗУ (Всего 4 ГБ, доступно 2.5, 30% = 750 Мб):  
`innodb_buffer_pool_size = 750M`  

Размер файла логов операций 100 Мб:  
`innodb_log_file_size = 100M` 

