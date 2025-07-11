# 09 Полезные запросы (I): Запросы цен токенов ERC20

В ежедневном анализе данных мы обычно сталкиваемся с некоторыми общими запросами, такими как отслеживание изменений цены токена ERC20, запрос баланса различных токенов ERC20, хранящихся на определенном адресе, и т. д. В справочной документации платформы Dune разделы [некоторые полезные панели данных](https://dune.com/docs/reference/wizard-tools/helpful-dashboards/) и [утилитарные запросы](https://dune.com/docs/reference/wizard-tools/utility-queries/) приводят некоторые примеры, вы можете обратиться к ним. В этом руководстве мы объединяем некоторые типичные потребности, с которыми мы сталкиваемся в своей повседневной жизни, и сортируем для вас некоторые примеры запросов.

## Запрос последней цены отдельного токена ERC20

Токены ERC20 используются в широком разнообразии блокчейн-приложений. DeFi-инициативы облегчают торговлю токенами ERC20. Другие проекты вознаграждают своих сторонников, ранних последователей и команды разработчиков посредством планов распределения и раздачи в обмен на токены ERC20. Данные о ценах для нескольких токенов ERC20 можно найти на таких сайтах, как [CoinGecko](https://www.coingecko.com/). Таблицы 'prices.usd' и 'prices.usd_latest' в Dune позволяют аналитикам данных легко извлекать текущую рыночную стоимость наиболее популярных токенов ERC20 на каждом блокчейне. Существует таблица под названием [prices.usd](https://dune.com/docs/reference/tables/prices/), которая отслеживает цены различных токенов ERC20 минута за минутой. Чтобы облегчить такие действия, как обобщение и сравнение при исследовании проектов, связанных с токенами ERC20, мы можем объединить данные о ценах, чтобы преобразовать количество различных токенов в сумму, указанную в долларах США.

**Получение последней цены отдельного токена ERC20**

Цены в таблице 'prices.usd' регистрируются ежеминутно. Получение самой последней записи зависит от символа токена и соответствующего блокчейна, с которым он связан. В случае, если доступен адрес контракта, его также можно использовать для целей запроса. База данных `usd_latest` предназначена для хранения последней цены каждого токена. Каждый токен представлен одной строкой в таблице. Ниже приведены методы, которые можно использовать для получения последней цены отдельного токена, используя WETH в качестве иллюстративного примера. Для повышения производительности запроса мы ограничиваем извлечение последней части данных, поскольку информация о ценах хранится в одной записи на токен каждую минуту, что приводит к большому количеству записей для каждого токена. Периодически может существовать определенное временное отставание. В настоящем случае мы получаем самую последнюю запись данных за последние шесть часов, чтобы определить доступность цены.

**Используйте стоимость токена для чтения последней информации о ценах в таблице `prices.usd`:**

```sql
select * from prices.usd
where symbol = 'WETH'
    and blockchain = 'ethereum'
    and minute >= now() - interval '6' hour
order by minute desc
limit 1
```

**Используйте адрес смарт-контракта токена для чтения последней цены в таблице `prices.usd`:**

```sql
select * from prices.usd
where contract_address = 0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2   -- WETH
    and minute >= now() - interval '6' hour
order by minute desc
limit 1
```

**Чтение последней информации о ценах из таблиц `prices.usd_latest`:**

```sql
select * from prices.usd_latest
where symbol = 'WETH'
    and blockchain = 'ethereum'
```

Запрос проще для чтения из таблицы `prices.usd_latest`, но поскольку она фактически является представлением таблицы `prices.usd`, ее выполнение немного менее эффективно.
Исходный код справки: [prices_usd _latest](https://github.com/dizunalytics/spellbook/blob/main/models/prices_usd.latest.sql)
## Проверьте последние цены на несколько токенов ERC20

Когда нам нужно получить последние цены на несколько токенов одновременно, проявляется удобство таблицы `prices.usd_latest`. Здесь мы возьмем последний запрос цены WETH, WBTC и USDC в качестве примера.


**Прочитайте последнюю информацию о ценах на несколько токенов из таблицы `prices.usd_latest`:**

``` sql
select * from prices.usd_latest
where symbol in ('WETH', 'WBTC', 'USDC')
    and blockchain = 'ethereum'
```

**Прочитайте последнюю информацию о ценах на несколько токенов из таблицы `prices.usd`:**

``` sql
select symbol, decimals, price, minute
from (
    select row_number() over (partition by symbol order by minute desc) as row_num, *
    from prices.usd
    where symbol in ('WETH', 'WBTC', 'USDC')
        and blockchain = 'ethereum'
        and minute >= now() - interval '6' hour
    order by minute desc
) p
where row_num = 1
```

Поскольку мы хотим получить последние цены на несколько токенов одновременно, мы не можем просто использовать предложение `limit` для ограничения количества результатов, чтобы получить желаемые результаты. Нам фактически нужно вернуть первую запись после каждой различной монеты, отсортированной в порядке убывания по полю `minute`. В приведенном выше запросе мы использовали `row_number() over (partition by symbol order by minute desc) as row_num` для создания нового столбца. Значения в этом столбце группируются по `symbol` и сортируются в порядке убывания по полю `minute` - то есть каждая различная монета будет генерировать свою последовательность значений номера строки, таких как 1, 2, 3, 4 и т. д. Мы помещаем это в подзапрос и фильтруем запись `where row_num = 1` во внешнем запросе, которая является последней записью для каждой монеты. Этот метод кажется немного сложным, но подобные запросы часто используются в практических приложениях, и новые столбцы генерируются с помощью функции `row_number()`, а затем используются для фильтрации данных.

## Запрос средней ежедневной цены отдельного токена ERC20

Когда нам нужно запросить среднюю цену токена ERC20 каждый день, мы можем использовать только таблицу `prices.usd`. Установив диапазон дат для запроса цены (или взяв данные за все даты без указания диапазона), суммируя по дням и используя функцию `avg()` для получения среднего значения, мы можем получить данные о цене по дням. SQL-запрос выглядит следующим образом:

``` sql
select date_trunc('day', minute) as block_date,
    avg(price) as price
from prices.usd
where symbol = 'WETH'
    and blockchain = 'ethereum'
    and minute >= date('2023-01-01')
group by 1
order by 1
```

Если нам нужно вернуть другие поля одновременно, мы можем добавить их в список SELECT и добавить их в предложение GROUP BY одновременно. Это связано с тем, что при использовании предложения `group by` поля, которые появляются в списке SELECT, также должны появляться в предложении GROUP BY, если они не являются агрегатными функциями. Модифицированный SQL-запрос выглядит следующим образом:

``` sql
select date_trunc('day', minute) as block_date,
    symbol,
    decimals,
    contract_address,
    avg(price) as price
from prices.usd
where symbol = 'WETH'
    and blockchain = 'ethereum'
    and minute >= date('2023-01-01')
group by 1, 2, 3, 4
order by 1
```
## Запрос среднедневной цены нескольких токенов ERC20

Аналогично, мы можем запрашивать среднюю цену группы токенов ERC20 каждый день в одно и то же время, просто поместите символ запрашиваемого токена в условный оператор `in ()`. SQL-запрос выглядит следующим образом:

```sql
select date_trunc('day', minute) as block_date,
    symbol,
    decimals,
    contract_address,
    avg(price) as price
from prices.usd
where symbol in ('WETH', 'WBTC', 'USDC')
    and blockchain = 'ethereum'
    and minute >= date('2022-10-01')
group by 1, 2, 3, 4
order by 2, 1   -- Сортировка по символу в первую очередь
```

## Рассчитать цену по записям о свопах DeFi

Таблица данных о ценах `prices.usd` на Dune поддерживается через spellbook, которая не включает информацию о ценах для всех токенов на всех поддерживаемых блокчейнах. Особенно, когда новый токен ERC20 только выпущен и добавлен на DEX (например, XEN), список цен Dune не будет автоматически отображать данные этого токена. В этом случае мы можем читать данные о свопах в проекте DeFi, например, данные о свопах в Uniswap, рассчитывать цену обмена между соответствующим токеном и USDC (или WETH), а затем конвертировать данные о ценах USDC или WETH, чтобы получить цену в долларах США. Пример запроса приведен ниже:

``` sql
with xen_price_in_usdc as (
    select date_trunc('hour', evt_block_time) as block_date,
        'XEN' as symbol,
        '0x06450dee7fd2fb8e39061434babcfc05599a6fb8' as contract_address, -- XEN
        18 as decimals,
        avg(amount1 / amount0) / pow(10, (6-18)) as price   --USDC: 6 decimals, XEN: 18 decimals
    from (
        select contract_address,
            abs(amount0) as amount0,
            abs(amount1) as amount1,
            evt_tx_hash,
            evt_block_time
        from uniswap_v3_ethereum.Pair_evt_Swap
        where contract_address = '0x353bb62ed786cdf7624bd4049859182f3c1e9e5d'   -- XEN-USDC 1.00% Pair
            and evt_block_time > '2022-10-07'
            and evt_block_time > now() - interval '30 days'
    ) s
    group by 1, 2, 3, 4
),

usdc_price as (
    select date_trunc('hour', minute) as block_date,
        avg(price) as price
    from prices.usd
    where contract_address = '0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48'   -- USDC
        and minute > '2022-10-07'
        and minute > now() - interval '30 days'
    group by 1
)

select x.block_date,
    x.price * u.price as price_usd
from xen_price_in_usdc x
inner join usdc_price u on x.block_date = u.block_date
order by x.block_date
```

Приведенный выше запрос является практическим применением в панели данных проекта XEN Crypto. Ссылка для справки:
- панель данных: [XEN Crypto Overview](https://dune.com/sixdegree/xen-crypto-overview)
- Запрос: [XEN - price trend](https://dune.com/queries/1382200)
## Рассчитать цену из таблицы заклинаний транзакций DeFi

Если соответствующие данные транзакций DeFi уже интегрированы в таблицу `dex.trades`, будет легче использовать эту таблицу для расчета цены. Мы можем разделить `amount_usd` на `token_bought_amount` или `token_sold_amount`, чтобы получить цену соответствующего токена в долларах США. В качестве примера возьмем USDC-WETH 0.30% на Uniswap V3, SQL-запрос для расчета последней цены WETH выглядит следующим образом:

``` sql
with trade_detail as (
    select block_time,
        tx_hash,
        amount_usd,
        token_bought_amount,
        token_bought_symbol,
        token_sold_amount,
        token_sold_symbol
    from dex.trades
    where project_contract_address = 0x8ad599c3a0ff1de082011efddc58f1908eb6e6d8
        and block_date >= now() - interval '3' day
    order by block_time desc
    limit 1000
)

select avg(
    case when token_bought_symbol = 'WETH' then amount_usd / token_bought_amount
        else amount_usd / token_sold_amount
    end
    ) as price
from trade_detail
```

## Рассчитать цену нативного токена (ETH)

Взяв Ethereum в качестве примера, его нативный токен ETH не является токеном ERC20, поэтому информации о цене ETH самой по себе нет в таблице `prices.usd`. Однако, токены WETH (Wrapped ETH) эквивалентны ETH, поэтому мы можем напрямую использовать данные о цене WETH.