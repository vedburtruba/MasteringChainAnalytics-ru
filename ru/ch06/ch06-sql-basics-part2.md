## 06 Основы SQL (II)

В разделе «Синтаксис SQL (I)» мы рассмотрели основы SQL, включая синтаксис инфраструктуры операторов запросов SQL, даты и времени, group by, with as и join. Далее мы продолжим знакомить с некоторыми общими основами SQL.

## Наиболее часто используемые функции даты и интервала

В контексте блокчейна данные записываются и хранятся в порядке возникновения транзакций. При проведении ежедневного анализа данных часто необходимо выполнять статистические операции над данными за определенные периоды времени. В предыдущем разделе мы представили функцию `date_trunc()`, которая позволяет усекать значения даты до указанных интервалов, таких как дни, недели или часы. Кроме того, существует несколько часто используемых функций и их соответствующее использование.

### 1. Функции Now() и Current_Date()

Функция `now()` используется для получения даты и времени текущей системы. Следует отметить, что она хранится внутренне со значениями часа, минуты и секунды, но редактор запросов Dune отображает по умолчанию только «час:минута». Когда нам нужно сопоставить поле даты с полем `minute` в таблице `prices.usd`, мы должны сначала усекать по минуте. В противном случае, может не быть правильно связан корректный записей о цене.

Функция `current_date()` используется для получения текущей даты (без часов, минут и секунд). При фильтрации данных по дате и времени, мы часто должны комбинировать эти функции и использовать соответствующие функции даты для получения точной даты или времени. Функция `current_date()` эквивалентна использованию `date_trunc('day', now())`, которая извлекает значение дня из результата `now()`. Вы также можете опустить круглые скобки в `current_date()` и написать ее как `current_date` напрямую.

```sql
select now() -- текущая дата и время
    ,current_date() -- текущая дата
    ,current_date   -- текущая дата
    ,date_trunc('day', now()) -- то же самое, что и current_date
```

### 2. Функции DateAdd(), Date_Add(), Date_Sub() и DateDiff()

Функция `dateadd(unit, value, expr)` добавляет единицу измерения даты и времени к выражению даты. Здесь «единицы измерения даты и времени» используют константы, наиболее часто используемые - HOUR, DAY, WEEK, MONTH и так далее. Значение может быть отрицательным числом, что означает, что соответствующая единица измерения даты и времени вычитается из следующего выражения. Это также и причина того, что функция `datesub()` не требуется и фактически недоступна, поскольку может использоваться отрицательное число для указания вычитания временного интервала.

Функция `date_add(startDate, numDays)` добавляет или вычитает указанное количество дней к выражению даты и возвращает другую дату. Параметр `numDays` для положительного числа дней после указанной даты для возврата к `startDate`, для отрицательного числа дней до указанной даты для возврата. Функция `date_sub (startDate, numDays)` похожа, но смысл обратный, отрицательное число указывает дату после возврата, а положительное число указывает предыдущую дату.

Функция `datediff(endDate, startDate)` возвращает количество дней между двумя выражениями даты. Если `endDate` после `startDate`, она возвращает положительное значение, до нее — отрицательное значение.

Пример SQL следующий:

```sql
select date_add('MONTH', 2, current_date) -- Добавить 2 месяца к текущей дате
    ,date_add('HOUR', 12, now()) -- Добавить 12 часов к текущей дате
    ,date_add('DAY', -2, current_date) -- Вычесть 2 дня из текущей даты
    ,date_add('DAY', 2, current_date) -- Добавить 2 дня к текущей дате
    ,date_add('DAY', -5, current_date) -- Вычесть 5 дней из текущей даты
    ,now() - interval '2' hour -- 2 часа назад
    ,current_date - interval '7' day -- 7 дней назад
    ,now() + interval '1' month -- 1 месяц после текущей даты
```

Для описания дополнительных функций, связанных с датой и временем, см. [Функции и операторы даты и времени](https://trino.io/docs/current/functions/datetime.html)
## Условные выражения Case, If

Когда требуется применить условную логику, можно использовать оператор `case`. Общий синтаксис оператора CASE: `CASE {WHEN cond1 THEN res1} [...] [ELSE def] END`. Этот оператор позволяет вычислить выражение при нескольких условиях и возвращает значение, соответствующее первому условию, которое вычисляется как True. Если ни одно из условий не выполняется, возвращается значение, указанное после `else`. Часть `else` является необязательной, и если она опущена, возвращается NULL.

Мы многократно использовали оператор CASE в разделе "Практический случай: Анализ доменного имени профиля создателя Lens". Некоторые выдержки из кода приведены ниже:

``` sql
-- ...пропустить некоторый код...

profiles_summary as (
    select (
            case
                when length(short_name) >= 20 then 20 -- если длина имени профиля больше 20, то установить значение 20
                else length(short_name) -- если длина имени профиля меньше 20, использовать исходную длину
            end) as name_length, -- переименовать колонку case в новое имя
        handle_type,
        count(*) as name_count
    from profile_created
    group by 1, 2
),

profiles_total as (
    select count(*) as total_profile_count,
        sum(case
                when handle_type = 'Pure Digits' then 1 -- если handle_type равен 'Pure Digits', вернуть 1
                else 0  -- иначе вернуть 0
            end
        ) as pure_digit_profile_count,
        sum(case 
                when handle_type = 'Pure Letters' then 1 
                else 0  
            end
        ) as pure_letter_profile_count
    from profile_created
)

-- ...пропустить некоторый код...
```

Как видите, с помощью оператора CASE мы можем гибко преобразовывать данные в соответствии с текущими потребностями для последующего статистического суммирования.

Ссылки на пример запроса:

- Запрос:[https://dune.com/queries/1535541](https://dune.com/queries/1535541)
- Описание: [Анализ доменного имени профиля создателя Lens](https://sixdegreelab.gitbook.io/mastering-chain-analytics/ru-men-jiao-cheng/06_pratical_case_lens_protocol)

Функция `if(cond, expr1, expr2)` возвращает одно из двух выражений, в зависимости от того, вычисляется ли условие как true или false. Если условие вычисляется как true, возвращается первое выражение, а если как false, возвращается второе выражение.

``` sql
select if(1 < 2, 'a', 'b') -- если результат условия истинный, вернуть 'a', иначе вернуть 'b'
    ,if('a' = 'A', 'case-insensitive', 'case-sensitive') 
 ```

## Общие функции для обработки строк

1. Функция Substring()

Когда есть определенные ситуации, когда нам приходится работать с исходной таблицей данных `transactions` или `logs` и декодировать `data` в ней, нам нужно извлечь часть строки из нее, а затем выполнить целевой процесс преобразования. В этом случае нам необходимо использовать функцию Substring. Синтаксис функции Substring — `substring( expr, pos [, len])` или `substring (expr FROM pos [FOR len] )`, которая в выражении `expr`, начиная с позиции `pos`, извлекает `len` символов и возвращает. Если `len` опущена, строка извлекается до конца строки.

2. Функция Concat() и оператор ||

Функция `concat(expr1, expr2 [, ...])` соединяет несколько выражений вместе. ) соединяет несколько выражений вместе и часто используется для связывания строк. Оператор `||` имеет ту же функцию, что и Concat.

``` sql
select concat('a', ' ', 'b', ' c') -- concat multi string
    , 'a' || ' ' || 'b' || ' c' -- same as concat
```

3. Функция Right()

Функция `right(str, len)` извлекает `len` символов с правого края строки `str`. В нашем случае исходная таблица данных, такая как `logs`, содержит связанные группы из 64 символа, хранящиеся в `data`. Для адреса контракта или адреса пользователя он представлен 40 символами. При сохранении он заполняется `0` слева, чтобы заполнить 64-битную длину. При извлечении адреса необходимо извлечь 40 символов с правого края и добавить префикс '0x' для восстановления правильного формата адреса.

Обратите внимание, что в Dune SQL прямое использование функции `right()` может вернуть синтаксическую ошибку, что можно решить, поместив имя функции в двойные кавычки, то есть используя `"right"()`. Поскольку этот метод громоздкий, можно использовать функцию substring с параметром отрицательной позиции начала для выполнения извлечения с правой стороны с легкостью.

Ниже приведен исчерпывающий пример использования вышеперечисленных функций. Этот пример декодирует записи перекрестной цепи в Arbitrum из таблицы `logs`, используя несколько методов.

``` sql
select date_trunc('day', block_time) as block_date, --truncate a timestamp to day
    concat('0x', "right"(substring(cast(data as varchar), 3 + 64 * 2, 64), 40)) as address, -- Extract the third part of the data column and convert it into an address, starting from the third character, each 64 characters as a group.
    concat('0x', "right"(substring(cast(data as varchar), 3 + 64 * 3, 64), 40)) as token, -- Extract part 4 of data and convert it to address
    concat('0x', substring(substring(cast(data as varchar), 3 + 64 * 3, 64), -40, 40)) as same_token, -- Extract part 4 of data and convert it to address
    substring(cast(data as varchar), 3 + 64 * 4, 64) as hex_amount, -- Extract part 5 of data column
    bytearray_to_uint256(bytearray_substring(data, 1 + 64 * 3, 64)) as amount, -- Extract part 5 of data column
    substring(cast(data as varchar), 3 + 64 * 5, 64) as to, -- Extract part 5 of data column
    substring(cast(data as varchar), 3 + 64 * 6, 64) as fee, -- Extract part 5 of data column
    substring(cast(data as varchar), 3 + 64 * 7, 64) as gas, -- Extract part 5 of data column
    substring(cast(data as varchar), 3 + 64 * 6, 64) as gasPrice, -- Extract part 5 of data column
    substring(cast(data as varchar), 3 + 64 * 1, 64) as value, -- Extract part 5 of data column
    substring(cast(data as varchar), 3 + 64 * 2, 64) as input, -- Extract part 5 of data column
    substring(cast(data as varchar), 3 + 64 * 0, 64) as nonce, -- Extract part 5 of data column
    substring(cast(data as varchar), 3 + 64 * 7, 64) as v, -- Extract part 5 of data column
    substring(cast(data as varchar), 3 + 64 * 8, 64) as r, -- Extract part 5 of data column
    substring(cast(data as varchar), 3 + 64 * 9, 64) as s, -- Extract part 5 of data column
    block_time >= now() - interval '30' day
limit 10
```

Связанные ссылки для приведенного выше запроса:

- Запрос: [https://dune.com/queries/1647016](https://dune.com/queries/1647016)
- Описание: [Функции и операторы строк](https://trino.io/docs/current/functions/string.html)

Okay, this is a great overview of window functions, particularly focusing on `ROW_NUMBER()` and `LAG/LEAD` equivalents (represented by `LAG/LEAD` in the examples, which are similar to `ROW_NUMBER()` in function).  Let's break down the information and how it can be applied, then I'm going to add some potential improvements and additional window functions.

**Key Takeaways & Summary**

*   **Window Functions:**  These functions perform calculations across a set of rows that are related to the current row. Unlike aggregate functions (like `SUM`, `AVG`, `COUNT`), window functions *don't* collapse rows; they return a value for each row based on the window of related rows.

*   **`LAG/LEAD` (through `ROW_NUMBER()` example):**  Allows you to access data from previous or subsequent rows within the window.  The provided example simulates this using `ROW_NUMBER()` and subtracting from the next `ROW_NUMBER()`.  It's essentially calculating a difference between consecutive rows based on a sorted order. This is very useful for calculating changes over time or comparing values.

*   **`ROW_NUMBER()`:**  Assigns a unique sequential integer to each row within a partition, based on a specified ordering. This is essential for ranking, limiting results within groups, or creating artificial sequences.

*   **Partitioning vs. Ordering:**  `PARTITION BY` defines the groups within which the window function operates.  `ORDER BY` specifies how the rows within each partition are ordered for calculations (like ranking or calculating differences).

**Improvements & Expansions**

1.  **True `LAG` and `LEAD` Functions:** The explanation correctly illustrates the effect using `ROW_NUMBER()` subtraction, but in most SQL dialects, `LAG` and `LEAD` are built-in functions.

    *   **`LAG(column, offset, default)`:**  Gets the value from the row *before* the current row.  `offset` determines how many rows back to look (default is 1). `default` provides a value if there is no previous row.
    *   **`LEAD(column, offset, default)`:**  Gets the value from the row *after* the current row.  `offset` determines how many rows ahead to look (default is 1). `default` provides a value if there is no next row.

    Example:

    ```sql
    SELECT
        contract_address,
        symbol,
        price,
        LAG(price, 1, 0) OVER (PARTITION BY contract_address ORDER BY minute) AS previous_price,
        LEAD(price, 1, 0) OVER (PARTITION BY contract_address ORDER BY minute) AS next_price
    FROM prices.usd
    WHERE contract_address IN ('some_token_address')
    LIMIT 10;  -- For demonstration
    ```

2.  **Other Useful Window Functions:**

    *   **`RANK()`, `DENSE_RANK()`, `NTILE()`:** Similar to `ROW_NUMBER()`, but they assign ranks based on the order.
        *   `RANK()`: Assigns ranks with gaps (e.g., 1, 2, 2, 4).
        *   `DENSE_RANK()`: Assigns ranks without gaps (e.g., 1, 2, 2, 3).
        *   `NTILE(n)`: Divides the rows into `n` groups and assigns a group number to each row.
    *   **`SUM()`, `AVG()`, `MIN()`, `MAX()`, `COUNT()` (with Windowing):** You can use these aggregate functions with `PARTITION BY` and `ORDER BY` to calculate aggregate values over a window of rows.
    *   **`FIRST_VALUE()`, `LAST_VALUE()`:** Return the first/last value in the window.

3.  **Clarification of `ROW_NUMBER()` and Gaps:**  While `ROW_NUMBER()` assigns consecutive integers, it's important to note that the order it assigns them in is *entirely* dependent on the `ORDER BY` clause.  If two rows have the same value in the ordering column, the assignment is non-deterministic (the database can choose the order arbitrarily).

4.  **Handling `NULL` Values:** Window functions generally handle `NULL` values according to the data type's behavior. Be mindful of how `NULL` values might affect your results, especially when using functions like `AVG` or `SUM`.

**Applying the Concepts - Examples**

*   **Calculate the change in price of a token between consecutive minutes:**
    ```sql
    SELECT
        contract_address,
        minute,
        price,
        price - LAG(price, 1, 0) OVER (PARTITION BY contract_address ORDER BY minute) AS price_change
    FROM prices.usd
    WHERE contract_address IN ('some_token_address')
    ORDER BY minute;
    ```
*   **Find the top 3 most active tokens (most transactions) in the last hour:**
    ```sql
    -- Assuming a table with transaction data: transactions(token_address, timestamp)
    SELECT
        token_address,
        COUNT(*) AS transaction_count
    FROM transactions
    WHERE timestamp >= NOW() - INTERVAL '1 hour'
    GROUP BY token_address
    ORDER BY transaction_count DESC
    LIMIT 3;
    ```
*   **Partition data into 5 groups based on transaction volume:**
    ```sql
    -- Assuming a table with transaction data
    SELECT
        token_address,
        transaction_count,
        NTILE(5) OVER (ORDER BY transaction_count DESC) AS volume_group
    FROM (
        SELECT
            token_address,
            COUNT(*) AS transaction_count
        FROM transactions
        GROUP BY token_address
    ) AS transaction_counts
    ORDER BY transaction_counts.transaction_count DESC;
    ```

**Key Advice**

*   **Understand Your Data and Goal:** Before using window functions, clearly define what you want to achieve and the relationships within your data.
*   **Experiment and Test:** Window functions can be complex. Test your queries thoroughly to ensure they produce the expected results.
*   **Read the Documentation:**  Refer to your specific database system's documentation for the exact syntax and behavior of window functions.



By incorporating these points, you can gain a more comprehensive understanding and practical application of window functions in your SQL queries.  Let me know if you'd like more examples or want to dive deeper into a particular aspect of window functions.
## Функция array_agg()

Если вы хотите объединить определенный столбец каждой строки в результирующем наборе данных запроса, вы можете использовать функцию array_agg(). Если вы хотите объединить несколько столбцов данных вместе (представьте себе экспорт результатов запроса в формате CSV), вы можете рассмотреть возможность использования метода конкатенации строк, описанного выше, для объединения нескольких столбцов данных в один столбец, а затем применить функцию array_agg(). Вот простой пример:

``` sql
select array_agg(contract_address) from
(
    select contract_address 
    from ethereum.logs
    where block_time >= current_date
    limit 10
) t
```

## Краткое содержание

Каждая база данных имеет десятки или даже сотни встроенных функций, и то, что мы представляем здесь, является лишь небольшой частью обычно используемых функций. Если вы хотите стать опытным аналитиком данных, мы настоятельно рекомендуем прочитать и понять использование каждой из встроенных функций здесь: [Функции Trino](https://trino.io/docs/current/functions.html).
## О нас

`Sixdegree` – это профессиональная команда по анализу данных на блокчейне. Наша миссия – предоставлять пользователям точные графики, анализ и инсайты на основе данных на блокчейне. Мы стремимся популяризировать анализ данных на блокчейне. Путем создания сообщества и написания учебных пособий, среди прочих инициатив, мы обучаем аналитиков данных на блокчейне, предоставляем ценный аналитический контент, продвигаем сообщество для построения слоя данных для блокчейна и развиваем таланты для широкого будущего приложений данных блокчейна. Добро пожаловать на площадку обмена опытом в сообществе!

- Веб-сайт: [sixdegree.xyz](https://sixdegree.xyz)
- Электронная почта: [contact@sixdegree.xyz](mailto:contact@sixdegree.xyz)
- Twitter: [twitter.com/SixdegreeLab](https://twitter.com/SixdegreeLab)
- Dune: [dune.com/sixdegree](https://dune.com/sixdegree)
- Github: [https://github.com/SixdegreeLab](https://github.com/SixdegreeLab)
