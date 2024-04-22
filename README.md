# Домашнее задание к занятию 2.«SQL»
## Введение
Перед выполнением задания вы можете ознакомиться с [дополнительными материалами](https://github.com/netology-code/virt-homeworks/blob/virt-11/additional/README.md).

## Задача 1
Используя Docker, поднимите инстанс PostgreSQL (версию 12) c 2 volume, в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose-манифест.

## Задача 2
В БД из задачи 1: 
- создайте пользователя test-admin-user и БД test_db;
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже);
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db;
- создайте пользователя test-simple-user;
- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE этих таблиц БД test_db.
  
Таблица orders:
- id (serial primary key);
- наименование (string);
- цена (integer).
  
Таблица clients:
- id (serial primary key);
- фамилия (string);
- страна проживания (string, index);
- заказ (foreign key orders).
  
Приведите:
- итоговый список БД после выполнения пунктов выше;
- описание таблиц (describe);
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db;
- список пользователей с правами над таблицами test_db.

## Задача 3
Используя SQL-синтаксис, наполните таблицы следующими тестовыми данными:
Таблица orders
|Наименование|цена|
|------------|----|
|Шоколад| 10 |
|Принтер| 3000 |
|Книга| 500 |
|Монитор| 7000|
|Гитара| 4000|

Таблица clients
|ФИО|Страна проживания|
|------------|----|
|Иванов Иван Иванович| USA |
|Петров Петр Петрович| Canada |
|Иоганн Себастьян Бах| Japan |
|Ронни Джеймс Дио| Russia|
|Ritchie Blackmore| Russia|

Используя SQL-синтаксис
- вычислите количество записей для каждой таблицы.
  Приведите в ответе:
  ```
  - запросы,
  - результаты их выполнения.

## Задача 4
Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys, свяжите записи из таблиц, согласно таблице:
|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения этих операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод этого запроса. 

Подсказка: используйте директиву `UPDATE`.

## Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 (используя директиву EXPLAIN).

Приведите получившийся результат и объясните, что значат полученные значения.

## Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. задачу 1).

Остановите контейнер с PostgreSQL, но не удаляйте volumes.

Поднимите новый пустой контейнер с PostgreSQL.

Восстановите БД test_db в новом контейнере.

Приведите список операций, который вы применяли для бэкапа данных и восстановления. 

---

Ответ: 

## Задача 1:
```
version: '3.8'

services:
  postgres:
    image: postgres:12
    container_name: postgres12
    restart: always
    ports:
      - 5432:5432
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: postgres
    volumes:
      - pg_data:/var/lib/postgresql/data
      - pg_backups:/backups

volumes:
  pg_data:
  pg_backups:
```
## Задача 2:

SQL-запросы:

```
CREATE DATABASE test_db;
CREATE ROLE "test-admin-user" SUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT LOGIN;

CREATE TABLE orders 
(
id integer, 
name text, 
price integer, 
PRIMARY KEY (id) 
);

CREATE TABLE clients 
(
	id integer PRIMARY KEY,
	lastname text,
	country text,
	booking integer,
	FOREIGN KEY (booking) REFERENCES orders (Id)
);

CREATE ROLE "test-simple-user" NOSUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT LOGIN;
GRANT SELECT ON TABLE public.clients TO "test-simple-user";
GRANT INSERT ON TABLE public.clients TO "test-simple-user";
GRANT UPDATE ON TABLE public.clients TO "test-simple-user";
GRANT DELETE ON TABLE public.clients TO "test-simple-user";
GRANT SELECT ON TABLE public.orders TO "test-simple-user";
GRANT INSERT ON TABLE public.orders TO "test-simple-user";
GRANT UPDATE ON TABLE public.orders TO "test-simple-user";
GRANT DELETE ON TABLE public.orders TO "test-simple-user";
```
Итоговый список БД после выполнения запросов и описание таблиц:

![1](https://github.com/Sawyer086/06_db_02/blob/main/2/1.jpg)

SQL-запрос для выдачи списка пользователей с правами над таблицами test_db:
```
SELECT grantee, table_name, privilege_type
FROM information_schema.table_privileges
WHERE table_catalog = 'test_db';
```
Список пользователей с правами над таблицами test_db:

![2](https://github.com/Sawyer086/06_db_02/blob/main/2/2.jpg)

## Задача 3:

```
insert into orders VALUES (1, 'Шоколад', 10), (2, 'Принтер', 3000), (3, 'Книга', 500), (4, 'Монитор', 7000), (5, 'Гитара', 4000);
insert into clients VALUES (1, 'Иванов Иван Иванович', 'USA'), (2, 'Петров Петр Петрович', 'Canada'), (3, 'Иоганн Себастьян Бах', 'Japan'), (4, 'Ронни Джеймс Дио', 'Russia'), (5, 'Ritchie Blackmore', 'Russia');
select count (*) from orders;
select count (*) from clients;

```
![3](https://github.com/Sawyer086/06_db_02/blob/main/2/3.jpg)

## Задача 4:
```
update  clients set booking = 3 where id = 1;
update  clients set booking = 4 where id = 2;
update  clients set booking = 5 where id = 3;

Запрос получения 1 -  учитывает наличие в таблице заказов данных:
select * from clients as c where  exists (select id from orders as o where c.booking = o.id) ;
Запрос получения 2 - проверяет на непустые значения у клиентов по заказам:
select * from clients where booking is not null;

```
![4](https://github.com/Sawyer086/06_db_02/blob/main/2/4.jpg)

## Задача 5:
```
EXPLAIN select * from clients as c where  exists (select id from orders as o where c.booking = o.id) ;
```
![5](https://github.com/Sawyer086/06_db_02/blob/main/2/5.jpg)

## Задача 6:

