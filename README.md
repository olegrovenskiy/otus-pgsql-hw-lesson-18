## ДЗ otus-pgsql-hw-lesson-18


Создана база для задания

    postgres-# \c lesson18
    You are now connected to database "lesson18" as user "postgres".
    lesson18-#
    lesson18-#

таблицы

    lesson18=# \dt
               List of relations
     Schema |   Name    | Type  |  Owner
    --------+-----------+-------+----------
     public | bus       | table | postgres
     public | driver    | table | postgres
     public | model_bus | table | postgres
    (3 rows)

    lesson18=# SELECT * FROM bus;
     id |      route       | id_model | id_driver
    ----+------------------+----------+-----------
      1 | Москва-Болшево   |        1 |         1
      2 | Москва-Пушкино   |        1 |         2
      3 | Москва-Ярославль |        2 |         3
      4 | Москва-Кострома  |        2 |         4
      5 | Москва-Волгорад  |        3 |         5
      6 | Москва-Иваново   |          |
    (6 rows)
    
    lesson18=# SELECT * FROM model_bus;
     id | name
    ----+-------
      1 | ПАЗ
      2 | ЛИАЗ
      3 | MAN
      4 | МАЗ
      5 | НЕФАЗ
    (5 rows)

    lesson18=# SELECT * FROM driver;
     id | first_name | second_name
    ----+------------+-------------
      1 | Иван       | Иванов
      2 | Петр       | Петров
      3 | Савелий    | Сидоров
      4 | Антон      | Шторкин
      5 | Олег       | Зажигаев
      6 | Аркадий    | Паровозов
    (6 rows)
    
    lesson18=#




### 1. Реализовать прямое соединение двух или более таблиц

Пример соединения 2-х таблиц по полю id_model

    lesson18=#
    lesson18=# SELECT *
    lesson18-# FROM bus b
    lesson18-# JOIN model_bus mb ON b.id_model = mb.id;
     id |      route       | id_model | id_driver | id | name
    ----+------------------+----------+-----------+----+------
      1 | Москва-Болшево   |        1 |         1 |  1 | ПАЗ
      2 | Москва-Пушкино   |        1 |         2 |  1 | ПАЗ
      3 | Москва-Ярославль |        2 |         3 |  2 | ЛИАЗ
      4 | Москва-Кострома  |        2 |         4 |  2 | ЛИАЗ
      5 | Москва-Волгорад  |        3 |         5 |  3 | MAN
    (5 rows)
    
    lesson18=#


    lesson18=# SELECT *
    lesson18-# FROM bus b, model_bus mb
    lesson18-# WHERE b.id_model = mb.id;
     id |      route       | id_model | id_driver | id | name
    ----+------------------+----------+-----------+----+------
      1 | Москва-Болшево   |        1 |         1 |  1 | ПАЗ
      2 | Москва-Пушкино   |        1 |         2 |  1 | ПАЗ
      3 | Москва-Ярославль |        2 |         3 |  2 | ЛИАЗ
      4 | Москва-Кострома  |        2 |         4 |  2 | ЛИАЗ
      5 | Москва-Волгорад  |        3 |         5 |  3 | MAN
    (5 rows)
    
    lesson18=#

Пример объяснения запроса

    EXPLAIN
    SELECT *
    FROM bus b, model_bus mb
    WHERE b.id_model = mb.id;


        lesson18=#     EXPLAIN
        lesson18-#     SELECT *
        lesson18-#     FROM bus b, model_bus mb
        lesson18-#     WHERE b.id_model = mb.id;
                                      QUERY PLAN
        -----------------------------------------------------------------------
         Hash Join  (cost=1.14..30.51 rows=32 width=77)
           Hash Cond: (mb.id = b.id_model)
           ->  Seq Scan on model_bus mb  (cost=0.00..22.70 rows=1270 width=36)
           ->  Hash  (cost=1.06..1.06 rows=6 width=41)
                 ->  Seq Scan on bus b  (cost=0.00..1.06 rows=6 width=41)
        (5 rows)
        
        lesson18=#
        lesson18=#



### 2. Реализовать левостороннее (или правостороннее) соединение двух или более таблиц

Пример левосторонего и правосторонего объединения

    lesson18=#
    lesson18=# SELECT *
    lesson18-# FROM bus b
    lesson18-# LEFT JOIN model_bus mb ON b.id_model = mb.id;
     id |      route       | id_model | id_driver | id | name
    ----+------------------+----------+-----------+----+------
      1 | Москва-Болшево   |        1 |         1 |  1 | ПАЗ
      2 | Москва-Пушкино   |        1 |         2 |  1 | ПАЗ
      3 | Москва-Ярославль |        2 |         3 |  2 | ЛИАЗ
      4 | Москва-Кострома  |        2 |         4 |  2 | ЛИАЗ
      5 | Москва-Волгорад  |        3 |         5 |  3 | MAN
      6 | Москва-Иваново   |          |           |    |
    (6 rows)
    
    lesson18=#



    lesson18=# SELECT *
    lesson18-# FROM bus b
    lesson18-# RIGHT JOIN model_bus mb ON b.id_model = mb.id;
     id |      route       | id_model | id_driver | id | name
    ----+------------------+----------+-----------+----+-------
      1 | Москва-Болшево   |        1 |         1 |  1 | ПАЗ
      2 | Москва-Пушкино   |        1 |         2 |  1 | ПАЗ
      3 | Москва-Ярославль |        2 |         3 |  2 | ЛИАЗ
      4 | Москва-Кострома  |        2 |         4 |  2 | ЛИАЗ
      5 | Москва-Волгорад  |        3 |         5 |  3 | MAN
        |                  |          |           |  4 | МАЗ
        |                  |          |           |  5 | НЕФАЗ
    (7 rows)
    
    lesson18=#


EXPLAIN
SELECT *
FROM bus b
RIGHT JOIN model_bus mb ON b.id_model = mb.id;

        lesson18=#
        lesson18=# EXPLAIN
        lesson18-# SELECT *
        lesson18-# FROM bus b
        lesson18-# RIGHT JOIN model_bus mb ON b.id_model = mb.id;
                                      QUERY PLAN
        -----------------------------------------------------------------------
         Hash Left Join  (cost=1.14..30.51 rows=1270 width=77)
           Hash Cond: (mb.id = b.id_model)
           ->  Seq Scan on model_bus mb  (cost=0.00..22.70 rows=1270 width=36)
           ->  Hash  (cost=1.06..1.06 rows=6 width=41)
                 ->  Seq Scan on bus b  (cost=0.00..1.06 rows=6 width=41)
        (5 rows)
        
        lesson18=#



### 4. Реализовать кросс соединение двух или более таблиц

        lesson18=# SELECT *
        lesson18-# FROM bus b
        lesson18-# CROSS JOIN model_bus mb;
         id |      route       | id_model | id_driver | id | name
        ----+------------------+----------+-----------+----+-------
          1 | Москва-Болшево   |        1 |         1 |  1 | ПАЗ
          2 | Москва-Пушкино   |        1 |         2 |  1 | ПАЗ
          3 | Москва-Ярославль |        2 |         3 |  1 | ПАЗ
          4 | Москва-Кострома  |        2 |         4 |  1 | ПАЗ
          5 | Москва-Волгорад  |        3 |         5 |  1 | ПАЗ
          6 | Москва-Иваново   |          |           |  1 | ПАЗ
          1 | Москва-Болшево   |        1 |         1 |  2 | ЛИАЗ
          2 | Москва-Пушкино   |        1 |         2 |  2 | ЛИАЗ
          3 | Москва-Ярославль |        2 |         3 |  2 | ЛИАЗ
          4 | Москва-Кострома  |        2 |         4 |  2 | ЛИАЗ
          5 | Москва-Волгорад  |        3 |         5 |  2 | ЛИАЗ
          6 | Москва-Иваново   |          |           |  2 | ЛИАЗ
          1 | Москва-Болшево   |        1 |         1 |  3 | MAN
          2 | Москва-Пушкино   |        1 |         2 |  3 | MAN
          3 | Москва-Ярославль |        2 |         3 |  3 | MAN
          4 | Москва-Кострома  |        2 |         4 |  3 | MAN
          5 | Москва-Волгорад  |        3 |         5 |  3 | MAN
          6 | Москва-Иваново   |          |           |  3 | MAN
          1 | Москва-Болшево   |        1 |         1 |  4 | МАЗ
          2 | Москва-Пушкино   |        1 |         2 |  4 | МАЗ
          3 | Москва-Ярославль |        2 |         3 |  4 | МАЗ
          4 | Москва-Кострома  |        2 |         4 |  4 | МАЗ
          5 | Москва-Волгорад  |        3 |         5 |  4 | МАЗ
          6 | Москва-Иваново   |          |           |  4 | МАЗ
          1 | Москва-Болшево   |        1 |         1 |  5 | НЕФАЗ
          2 | Москва-Пушкино   |        1 |         2 |  5 | НЕФАЗ
          3 | Москва-Ярославль |        2 |         3 |  5 | НЕФАЗ
          4 | Москва-Кострома  |        2 |         4 |  5 | НЕФАЗ
          5 | Москва-Волгорад  |        3 |         5 |  5 | НЕФАЗ
          6 | Москва-Иваново   |          |           |  5 | НЕФАЗ
        (30 rows)
        
        lesson18=#



### 5. Реализовать полное соединение двух или более таблиц

        lesson18=#
        lesson18=# SELECT *
        lesson18-# FROM bus b
        lesson18-# FULL JOIN model_bus mb ON b.id_model = mb.id;
         id |      route       | id_model | id_driver | id | name
        ----+------------------+----------+-----------+----+-------
          1 | Москва-Болшево   |        1 |         1 |  1 | ПАЗ
          2 | Москва-Пушкино   |        1 |         2 |  1 | ПАЗ
          3 | Москва-Ярославль |        2 |         3 |  2 | ЛИАЗ
          4 | Москва-Кострома  |        2 |         4 |  2 | ЛИАЗ
          5 | Москва-Волгорад  |        3 |         5 |  3 | MAN
          6 | Москва-Иваново   |          |           |    |
            |                  |          |           |  4 | МАЗ
            |                  |          |           |  5 | НЕФАЗ
        (8 rows)
        
        lesson18=#




### 6. Реализовать запрос, в котором будут использованы разные типы соединений


        lesson18=#
        lesson18=# SELECT *
        lesson18-# FROM bus b
        lesson18-# FULL JOIN model_bus mb ON b.id_model = mb.id
        lesson18-# LEFT JOIN driver dr ON b.id_driver = dr.id;
         id |      route       | id_model | id_driver | id | name  | id | first_name | second_name
        ----+------------------+----------+-----------+----+-------+----+------------+-------------
          1 | Москва-Болшево   |        1 |         1 |  1 | ПАЗ   |  1 | Иван       | Иванов
          2 | Москва-Пушкино   |        1 |         2 |  1 | ПАЗ   |  2 | Петр       | Петров
          3 | Москва-Ярославль |        2 |         3 |  2 | ЛИАЗ  |  3 | Савелий    | Сидоров
          4 | Москва-Кострома  |        2 |         4 |  2 | ЛИАЗ  |  4 | Антон      | Шторкин
          5 | Москва-Волгорад  |        3 |         5 |  3 | MAN   |  5 | Олег       | Зажигаев
          6 | Москва-Иваново   |          |           |    |       |    |            |
            |                  |          |           |  4 | МАЗ   |    |            |
            |                  |          |           |  5 | НЕФАЗ |    |            |
        (8 rows)
        
        lesson18=#



### Статистика по таблицам

analyze bus;
select *
from pg_stats
where tablename = 'bus';


Пример статистики по таблице bus

        lesson18=# select *
        from pg_stats
        where tablename = 'bus';
        -[ RECORD 1 ]----------+------------------------------------------------------------------------------------------------
        schemaname             | public
        tablename              | bus
        attname                | id
        inherited              | f
        null_frac              | 0
        avg_width              | 4
        n_distinct             | -1
        most_common_vals       |
        most_common_freqs      |
        histogram_bounds       | {1,2,3,4,5,6}
        correlation            | 1
        most_common_elems      |
        most_common_elem_freqs |
        elem_count_histogram   |
        -[ RECORD 2 ]----------+------------------------------------------------------------------------------------------------
        schemaname             | public
        tablename              | bus
        attname                | route
        inherited              | f
        null_frac              | 0
        avg_width              | 29
        n_distinct             | -1
        most_common_vals       |
        most_common_freqs      |
        histogram_bounds       | {Москва-Болшево,Москва-Волгорад,Москва-Иваново,Москва-Кострома,Москва-Пушкино,Москва-Ярославль}
        correlation            | -0.028571429
        most_common_elems      |
        most_common_elem_freqs |
        elem_count_histogram   |
        -[ RECORD 3 ]----------+------------------------------------------------------------------------------------------------
        schemaname             | public
        tablename              | bus
        attname                | id_model
        inherited              | f
        null_frac              | 0.16666667
        avg_width              | 4
        n_distinct             | -0.5
        most_common_vals       | {1,2}
        most_common_freqs      | {0.33333334,0.33333334}
        histogram_bounds       |
        correlation            | 1
        most_common_elems      |
        most_common_elem_freqs |
        elem_count_histogram   |
        -[ RECORD 4 ]----------+------------------------------------------------------------------------------------------------
        schemaname             | public
        tablename              | bus
        attname                | id_driver
        inherited              | f
        null_frac              | 0.16666667
        avg_width              | 4
        n_distinct             | -0.8333333
        most_common_vals       |
        most_common_freqs      |
        histogram_bounds       | {1,2,3,4,5}
        correlation            | 1
        most_common_elems      |
        most_common_elem_freqs |
        elem_count_histogram   |
        
        lesson18=#
        

