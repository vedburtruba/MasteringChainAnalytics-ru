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
