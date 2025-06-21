# 08 Практика – Создание панели управления Lens (II)

В первой части этого учебного пособия мы познакомили вас с протоколом Lens и создали предварительную панель управления для него, анализируя общее количество транзакций и общее количество пользователей, количество транзакций и количество уникальных пользователей в день, данные профиля создателя, анализ доменных имен Lens, поиск зарегистрированных доменных имен и другой связанный контент. Давайте добавим новые запросы и визуализации на эту панель управления. Мы проанализируем и добавим следующее содержимое: создание нескольких профилей с одного адреса, данные о подписке, данные о публикациях, данные о комментариях, данные о коллекциях, данные о зеркалировании, комплексное управление профилями и комплексное управление обычными адресами пользователей.

## Создание нескольких профилей с одного адреса

Протокол Lens позволяет создавать несколько профилей для одного адреса. Мы можем написать запрос для подсчета распределения данных адресов с несколькими созданными профилями. В следующем запросе мы сначала используем CTE profile_created для получения деталей всех созданных профилей, а затем используем multiple_profiles_addresses для подсчета количества профилей, созданных для каждого адреса. Наконец, мы используем оператор CASE для возврата агрегированной статистики, сортируя каждый адрес по количеству созданных профилей.

``` sql
with profile_created as (
    select json_value(vars, 'lax $.to') as user_address,
        json_value(vars, 'lax $.handle') as handle_name,
        replace(json_value(vars, 'lax $.handle'), '.lens', '') as short_name,
        call_block_time,
        output_0 as profile_id,
        call_tx_hash
    from lens_polygon.LensHub_call_createProfile
    where call_success = true    
),

multiple_profiles_addresses as (
    select user_address,
        count(profile_id) as profile_count
    from profile_created
    group by 1
    order by 2 desc
)

select (case when profile_count >= 10 then '10+ Profiles'
            when profile_count >= 3 then '5+ Profiles'
            when profile_count = 2 then '2 Profiles'
            else '1 Profile'
        end) as profile_count_type,
    count(user_address) as user_address_count,
    sum(profile_count) as profile_count
from multiple_profiles_addresses
group by 1
```

При выполнении этого вида статистики данных, мы обычно также нуждаемся в получении некоторых статистических значений типа Counter, таких как общее количество адресов, которые создали несколько профилей, сколько профилей создано этими адресами и доля этих профилей от общего количества созданных профилей и т.д. Вышеуказанный подзапрос CTE может быть использован при запросе этих данных, поэтому мы внесли небольшие изменения и добавили два дополнительных CTE для подсчета значений этих Counter типов. Добавьте визуальную диаграмму для этого запроса и добавьте ее на панель управления данными соответственно. Эффект отображения выглядит следующим образом:

![](img/ch08_image_09.png)

Ссылка на референс для вышеуказанного запроса на Dune:
- [https://dune.com/queries/1562662](https://dune.com/queries/1562662)
- [https://dune.com/queries/1553030](https://dune.com/queries/1553030)
## Анализ активности пользователей Lens: статистика и графики

### Статистика по количеству публикаций активных профилей

Ежедневное количество новых публикаций пользователей Lens – важный показатель общего уровня активности. Мы пишем запрос для подсчета количества публикаций в день. CTE `post_data` в этом запросе точно такой же, как и раньше, поэтому мы опускаем ее детали в приведенном ниже коде. Поскольку мы также хотим подсчитать количество публикаций в день и вернуть накопленное количество публикаций, мы определяем CTE `post_daily_summary` в качестве промежуточного этапа для упрощения понимания SQL-кода. Соответствующий SQL-код выглядит следующим образом:

```sql
with post_data as (
    -- Получение данных о публикациях из таблиц LensHub_call_post и LensHub_call_postWithSig
),

post_daily_summary as (
    select date_trunc('day', call_block_time) as block_date,
        count(*) post_count,
        count(distinct profile_id) as profile_count
    from post_data
    group by 1
)

select block_date,
    post_count,
    profile_count,
    sum(post_count) over (order by block_date) as accumulate_post_count
from post_daily_summary
order by block_date
```

Отображение после визуализации результатов запроса и добавления их на информационную панель выглядит следующим образом:

[image_11.png](img/image_11.png)

Ссылка на этот запрос на Dune:
- [https://dune.com/queries/1555124](https://dune.com/queries/1555124)

### Топ-100 активных профилей по количеству публикаций за 30 дней

Аналогично, нас может интересовать статистика по профилям с наибольшим количеством публикаций за последний период. Для этого нам нужно просто добавить условия фильтрации по дате, чтобы фильтровать публикации за последние 30 дней в указанном выше CTE `post_data`, а затем выполнить сводную статистику по дате. SQL-код выглядит следующим образом:

```sql
with post_data as (
    select call_block_time,
        call_tx_hash,
        output_0 as post_id,
        json_value(vars, 'lax $.profileId') as profile_id, -- Извлечение элемента JSON строки
        json_value(vars, 'lax $.contentURI') as content_url,
        json_value(vars, 'lax $.collectModule') as collection_module,
        json_value(vars, 'lax $.referenceModule') as reference_module
    from lens_polygon.LensHub_call_post
    where call_success = true
        and call_block_time >= now() - interval '30' day
    
    union all
    
    select call_block_time,
        call_tx_hash,
        output_0 as post_id,
        json_value(vars, 'lax $.profileId') as profile_id, -- Извлечение элемента JSON строки
        json_value(vars, 'lax $.contentURI') as content_url,
        json_value(vars, 'lax $.collectModule') as collection_module,
        json_value(vars, 'lax $.referenceModule') as reference_module
    from lens_polygon.LensHub_call_postWithSig
    where call_success = true
        and call_block_time >= now() - interval '30' day
)

select profile_id,
    count(*) as post_count
from post_data
group by 1
order by 2 desc
limit 100
```

Мы можем добавить гистограмму для отображения количества публикаций 100 аккаунтов с наибольшим количеством публикаций за последние 30 дней и добавить таблицу для вывода подробностей. Отображение после добавления соответствующей диаграммы на информационную панель выглядит следующим образом:

![](img/ch08_image_12.png)

Ссылка на этот запрос на Dune:
- [https://dune.com/queries/1559981](https://dune.com/queries/1559981)
