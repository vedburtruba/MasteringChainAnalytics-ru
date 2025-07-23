# 11 Полезные запросы (III): пользовательские данные, последовательность чисел, массив, JSON и т.д.

В первых двух частях общих запросов мы представили некоторые общие методы запросов, такие как запрос цены, держатель и баланс, удерживаемый токенами ERC20. В этом разделе мы рассмотрим некоторые другие распространенные запросы.

## Таблица пользовательских данных с использованием CTE

Dune V2 в настоящее время не поддерживает пользовательские таблицы и представления. Для некоторых данных из внешних источников или небольшого количества данных, созданных вручную, мы можем рассмотреть возможность использования CTE для создания пользовательского списка данных внутри запроса. Это позволяет поддерживать пользовательские таблицы CTE с тысячами строк всего лишь с несколькими полями и они будут успешно выполняться, пока не превысят максимальный размер запроса Dune. Существует два способа настройки таблиц CTE:

Пример первого синтаксиса:
``` sql
with raydium_lp_pairs(account_key, pair_name) as (
    values
    ('58oQChx4yWmvKdwLLZzBi4ChoCc2fqCUWBkwMihLYQo2', 'SOL/USDC'),
    ('7XawhbbxtsRcQA8KTkHT9f9nc6d69UwqCDh6U5EEbEmX', 'SOL/USDT'),
    ('AVs9TA4nWDzfPJE9gGVNJMVhcQy3V9PGazuz33BfG2RA', 'RAY/SOL'),
    ('6UmmUiYoBjSrhakAobJw8BvkmJtDVxaeBtbt7rxWo1mg', 'RAY/USDC'),
    ('DVa7Qmb5ct9RCpaU7UTpSaf3GVMYz17vNVU67XpdCRut', 'RAY/USDT'),
    ('GaqgfieVmnmY4ZsZHHA6L5RSVzCGL3sKx4UgHBaYNy8m', 'RAY/SRMSOL'),
    ('6a1CsrpeZubDjEJE9s1CMVheB6HWM5d7m1cj2jkhyXhj', 'STSOL/USDC'),
    ('43UHp4TuwQ7BYsaULN1qfpktmg7GWs9GpR8TDb8ovu9c', 'APEX4/USDC')
)

select * from raydium_lp_pairs
```

Пример второго синтаксиса:

``` sql
with token_plan as (
    select token_name, hook_amount from (
        values
        ('Token Type','BEP-20 on BNB Chain'),
        ('Total Token Supply','500,000,000 HOOK'),
        ('Private Sale Allocation','100,000,000 HOOK'),
        ('Private Sale Token Price','0.06 USD to 0.12 USD / HOOK'),
        ('Private Sale Amount Raised','~ 6,000,000 USD'),
        ('Binance Launchpad Sale Allocation','25,000,000 HOOK'),
        ('Binance Launchpad Sale Price','0.10 USD / HOOK'),
        ('Binance Launchpad Amount to be Raised','2,500,000 USD'),
        ('Initial Circ. Supply When Listed on Binance','50,000,000 HOOK (10.00%)')
    ) as tbl(token_name, hook_amount)
)

select * from token_plan
```

Конечно, с помощью второго синтаксиса вы можете опустить определение CTE и использовать запрос SELECT напрямую, если вам нужно вернуть только эту часть пользовательских данных.

Пример ссылки на вышеуказанный запрос:
- [https://dune.com/queries/781862](https://dune.com/queries/781862)
- [https://dune.com/queries/1650640](https://dune.com/queries/1650640)

В связи с упомянутыми выше ограничениями выполнение может не удаться, если строк слишком много. Вам придется дублировать один и тот же код CTE для каждого запроса, что относительно неудобно. Для больших объемов данных, многократно, для долгосрочного использования и т.д., вам все же следует рассмотреть возможность создания таблицы заклинаний путем отправки PR в Spellbook.
