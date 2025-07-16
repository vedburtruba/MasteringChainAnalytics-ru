## 10 Полезных запросов (II): Держатели токенов, общая эмиссия, баланс аккаунта

В части 1 полезных запросов мы в основном объясняли различные способы запроса цены токенов ERC20. В этой части мы рассмотрим, как получить данные о держателях токенов, общую эмиссию и баланс аккаунта.

Для этого необходимо узнать адрес контракта токена, который вас интересует. Например, для FTT токен находится по адресу `0x50d1c9771902476076ecfc8b2a83ad6b9355a4c9`.

Сначала узнаем баланс аккаунта. Данные о балансе аккаунта могут быть получены с помощью SQL запроса, который объединяет все транзакции, связанные с токеном.

Затем мы получаем данные о держателях токенов. Это можно сделать, агрегируя информацию о балансе аккаунта для каждого адреса.

И, наконец, мы можем получить информацию об общей эмиссии токенов. Это делается путем суммирования всех балансов аккаунтов.

На практике это может быть сложной задачей, особенно для токенов с большим количеством держателей. Однако, используя правильные инструменты и методы, вы можете получить ценные данные о токенах ERC20.

Например, можно использовать CTE (Common Table Expression) для структурирования запроса и удобства дальнейшей обработки данных.

```sql
with transfer_detail as (
    select evt_block_time,
        evt_tx_hash,
        contract_address,
        "to" as address,
        cast(value as decimal(38, 0)) as amount
    from erc20_ethereum.evt_Transfer
    where contract_address = '0x50d1c9771902476076ecfc8b2a83ad6b9355a4c9'
    
    union all
    
    select evt_block_time,
        evt_tx_hash,
        contract_address,
        "from" as address,
        -1 * cast(value as decimal(38, 0)) as amount
    from erc20_ethereum.evt_Transfer
    where contract_address = '0x50d1c9771902476076ecfc8b2a83ad6b9355a4c9'
),

address_balance as (
    select address,
        sum(amount) as balance_amount
    from transfer_detail
    group by address
)

select count(*) as holder_count,
    sum(balance_amount / 1e18) as supply_amount
from address_balance
where balance_amount > 0
```

В данном запросе `1e18` - это эквивалент `power(10, 18)`.  Различные токены ERC20 могут иметь разные значения десятичных знаков, которые можно узнать из таблицы `tokens.erc20`.

При работе с большим количеством адресов, запрос может занимать продолжительное время. Данные о количестве держателей и общей эмиссии могут незначительно отличаться от данных, представленных на Etherscan, из-за разных критериев, используемых для определения минимального баланса.

Пример запроса: [https://dune.com/queries/1620179](https://dune.com/queries/1620179)
## Запрос адреса, удерживающего наибольшее количество токенов

В предыдущем запросе, который запрашивал количество держателей токенов и объем в обращении, мы агрегировали текущий баланс токенов каждого держателя по адресу. Следовательно, легко запросить адреса пользователей с наибольшим количеством токенов и их соответствующие активы. Вы можете либо Fork запроса для внесения изменений, либо скопировать код запроса и создать новый запрос. Поскольку мы запрашиваем для одного токена, мы можем заменить жестко заданный адрес токена параметром запроса `{{token_contract_address}}` и установить адрес контракта токена FTT выше как значение по умолчанию, что дает нам возможность запрашивать данные для любого токена. Следующий запрос возвращает 100 адресов, удерживающих наибольшее количество токенов:

``` sql
with transfer_detail as (
    select evt_block_time,
        evt_tx_hash,
        contract_address,
        "to" as address,
        cast(value as decimal(38, 0)) as amount
    from erc20_ethereum.evt_Transfer
    where contract_address = {{token_contract_address}}
    
    union all
    
    select evt_block_time,
        evt_tx_hash,
        contract_address,
        "from" as address,
        -1 * cast(value as decimal(38, 0)) as amount
    from erc20_ethereum.evt_Transfer
    where contract_address = {{token_contract_address}}
),

address_balance as (
    select address,
        sum(amount / 1e18) as balance_amount
    from transfer_detail
    group by address
)

select address,
    balance_amount
from address_balance
order by 2 desc
limit 100
```

Используя параметры адреса контракта токена FTT по умолчанию, вышеуказанный запрос возвращает 100 адресов, удерживающих наибольшее количество FTTS. Мы можем визуализировать столбчатую диаграмму, сравнивающую количество, удерживаемое топ-100 держателями. Поскольку разница в количестве очевидна, мы зарегистрируем данные Y-оси, выбрав опцию Логарифмического масштаба. Как показано ниже:

![](img/ch10_image_04.png)

Пример запроса: [https://dune.com/queries/1620917](https://dune.com/queries/1620917)
