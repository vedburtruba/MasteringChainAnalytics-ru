# Анализ данных абстракции аккаунтов

В предыдущей главе упоминалось, что система абстракции аккаунтов, реализованная ERC4337, включает обычных пользователей, сборщиков, получателей и фабрик кошельков. Таким образом, при анализе эффективности участников экосистемы в абстракции аккаунтов, речь идет больше о рассмотрении ончейн-метрик этих нескольких участников. Здесь мы используем таблицы в декодированных EntryPoint на платформе **[Dune](https://dune.com)** для анализа. Мы берем версию EntryPoint v0.6 в сети Polygon в качестве примера. Вы можете найти соответствующую таблицу в `Data explorer` -> `Decoded projects` -> `erc4337` слева.

## Базовая таблица

- `handleOps(function)`: выполнение пакета UserOperations
- `UserOperationEvent(event)`: событие, испускаемое после каждого успешного запроса
- `AccountDeployed(event)`: развертывание аккаунта "отправителя".
## Анализ пользователей

Начнем с самого простого анализа: сколько User Operations были инициированы пользователями. Каждая Operation, инициированная пользователем, будет генерировать запись UserOperationEvent, поэтому вычисление должно использовать таблицу UserOperationEvent. Обратите внимание, что это не таблица `handleOps`, потому что, как упоминалось ранее, `handleOps` будет передавать массив ops, что означает, что одна запись в таблице `handleOps` будет соответствовать нескольким записям в таблице UserOperationEvent.

```sql
--получить общее количество User Operations
select count(*) from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent

--ежедневное количество User Operations
select date_trunc('day', evt_block_time) as dt, count(*) as nums
from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent 
group by 1
```
![](img/daily-operation.png)


Для ежедневного анализа новых пользователей — то есть для расчета количества новых адресов кошельков, создаваемых каждый день — есть два способа. Один — посчитать экземпляры, в которых поле `initCode` в `handleOps` не пустое, а второй — посчитать ежедневное количество новых пользователей через событие AccountDeployed.

![](img/daily-new-user.png)
## Анализ бандлеров

Для **бандлеров** обычно необходимо анализировать количество бандлеров и долю рынка каждого бандлера, поскольку таблица `UserOperationEvent` не записывает адрес бандлера, поэтому нам необходимо получить адрес EOA, инициировавший эту транзакцию, через поле хеша таблицы `polygon.transactions`. Конечно, есть и другой способ, который заключается в получении поля получателя как адрес бандлера путем ассоциации с таблицей `handleOps`. Здесь мы используем первый способ.

``` sql
-- общее количество бандлеров
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(distinct bundler) from ops left join tx on ops.evt_tx_hash = tx.hash


-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(distinct bundler) from ops left join tx on ops.evt_tx_hash = tx.hash


-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;


-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;


-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;


-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;


-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;


-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;


-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundлер, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

-- получить количество операций, упакованных каждым бандлером
with ops as (
       select 'polygon' as chain, 'MATIC' as symbol,'v0.6' as version,evt_block_time, evt_tx_hash, actualGasCost 
       from erc437_polygon.EntryPoint_v0_6_evt_UserOperationEvent
)
, tx as (
     select 'polygon' as chain, "from" as bundler, hash, block_time, cast(gas_price as double) * gas_used as tx_fee 
     from polygon.transactions 
     where block_time > timestamp '2023-02-15'
)
select count(bundler) from ops left join tx on ops.evt_tx_hash = tx.hash group by bundler order by count(bundler) DESC;

## Анализ плательщика

Анализ плательщика относительно проще, чем анализ сборщика. Информация о плательщике каждой операции записывается в поле `paymaster` в таблице `UserOperationEvent`. Если значение поля `paymaster` равно `0000...0000`, это означает, что операция (userOp) не использует плательщика. Если оно не пустое, оно хранит адрес контракта плательщика. Плательщик обычно анализирует информацию, такую как количество плательщиков и количество операций, за которые каждый плательщик заплатил.

``` sql
-- ===== общее количество плательщиков =====
select count(distinct paymaster) from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent;

-- ===== Общее количество и доллары США операций, оплаченных каждым плательщиком ======
with priceUSD as (
    select * from prices.usd where minute > timestamp '2023-02-15'
        and symbol in ('WMATIC')
        and contract_address in (
            0x0d500b1d8e8ef31e21c99d1db9a6444d3adf1270 --polygon wmatic
            )
)
, op_price as (
    select 
        op.paymaster, 
        cast(op.actualGasCost as double)/1e18 as actualGasCost, 
        pu.price, 
        cast(op.actualGasCost as double)/1e18*pu.price as actualGasCostUSD 
    from erc4337_polygon.EntryPoint_v0_6_evt_UserOperationEvent op
    left join priceUSD pu on date_trunc('minute', op.evt_block_time) = pu.minute
)
select paymaster, count(*) as paid_ops,sum(actualGasCost) as paid_matic, sum(actualGasCostUSD) as paid_USD 
from op_price group by paymaster
```

![](img/paymaster.png)
