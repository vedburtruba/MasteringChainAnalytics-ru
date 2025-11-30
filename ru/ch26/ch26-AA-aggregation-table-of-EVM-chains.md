## AA Таблица агрегации цепочек EVM

Контракт `EntryPoint` ERC4337 был развернут в нескольких цепочках EVM. Вы можете просмотреть список всех цепочек, в которых был развернут контракт `EntryPoint`, здесь:

- v0.5: [https://blockscan.com/address/0x5fF137D4b0FDCD49DcA30c7CF57E578a026d2789](https://blockscan.com/address/0x5fF137D4b0FDCD49DcA30c7CF57E578a026d2789)
- v0.6: [https://blockscan.com/address/0x0576a174D229E3cFA37253523E645A78A0C91B57](https://blockscan.com/address/0x0576a174D229E3cFA37253523E645A78A0C91B57)

Если вы хотите проанализировать объединенные данные всех цепочек EVM, это, очевидно, будет слишком громоздко для написания отдельных анализов. Поэтому, основываясь на заклинании платформы dune, мы агрегировали данные контрактов EntryPoint двух версий на 9 основных блокчейнах в две новые таблицы данных. Эти таблицы в настоящее время могут быть непосредственно использованы на платформе dune для удобного анализа. Исходный код можно найти здесь:

[https://github.com/duneanalytics/spellbook/tree/main/models/account_abstraction/erc4337](https://github.com/duneanalytics/spellbook/tree/main/models/account_abstraction/erc4337)

- `account_abstraction_erc4337.userops`: содержит данные события UserOperationEvent из 9 цепочек, также объединенные с таблицей транзакций и таблицей цен для получения информации, такой как сборщик каждой `userOp` и плата за газ для этой транзакции.
- `account_abstraction_erc4337.userops`: содержит данные о выгодоприобретателях для газовой платы пользователя

Таблица агрегации цепочек EVM:

| Название | URL                                                                             |
| :-------- | :------------------------------------------------------------------------------ |
| v0.5      | [https://blockscan.com/address/0x5fF137D4b0FDCD49DcA30c7CF57E578a026d2789](https://blockscan.com/address/0x5fF137D4b0FDCD49DcA30c7CF57E578a026d2789) |
| v0.6      | [https://blockscan.com/address/0x0576a174D229E3cFA37253523E645A78A0C91B57](https://blockscan.com/address/0x0576a174D229E3cFA37253523E645A78A0C91B57) |
| Github | [https://github.com/duneanalytics/spellbook/tree/main/models/account_abstraction/erc4337](https://github.com/duneanalytics/spellbook/tree/main/models/account_abstraction/erc4337) |

Анализ станет намного проще. Например, анализ распределения пользовательских операций по 9 цепочек:

```sql
with total_op as (
    select blockchain as chain, * from account_abstraction_erc4337.userops 
)
select chain, count(*) as nums from total_op group by chain order by nums desc
```

![](img/agg-userop-distribution.png)

Для другого примера, анализ ежемесячной прибыли сборщиков:

```sql
with agg_ops as (
    select 
      row_number() over (partition by tx_hash order by userop_hash) as rn
    , sum(op_fee) over(PARTITION by tx_hash) as op_fee
    , sum(op_fee_usd) over(PARTITION by tx_hash) as op_fee_usd
    , tx_fee
    , tx_fee_usd
    , blockchain as chain
    , gas_symbol as symbol
    , block_time
    from account_abstraction_erc4337.userops 
)
, revenue as (
    select
        date_trunc('month', block_time) as dt
        , sum(op_fee_usd - tx_fee_usd) as BundlerRevenueUSD
    from agg_ops
    where rn = 1
    group by 1
)
select  dt, 
    sum(BundlerRevenueUSD) as TotalBundlerRevenueUSD,
    case 
        when sum(BundlerRevenueUSD)>=0 then 'Positive'
        when sum(BundlerRevenueUSD)<0 then 'Negative'
    end as PosNeg
        
from revenue group by 1

```

![](img/agg-monthly-revenue.png)
