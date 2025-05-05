## Быстрый старт

### Введение

Наш учебник ориентирован на практическое применение и написан в соответствии со сценариями и потребностями ежедневного анализа данных в блокчейне. В этой статье будет объяснен SQL-базовый уровень, который необходимо понимать перед началом создания панелей мониторинга данных. Этот учебник предназначен для начинающих, в первую очередь для пользователей без опыта анализа данных. Мы предполагаем, что у вас нет опыта написания SQL-запросов. Пользователи, знакомые с SQL, но не знакомые с платформой Dune, также могут быстро просмотреть этот учебник. Этот учебник в основном включает в себя введение в платформу Dune, быстрый старт SQL-запросов и многое другое. В следующем учебнике мы будем писать запросы вместе, создавать визуальные диаграммы и использовать эти диаграммы для создания панелей мониторинга данных. Мы считаем, что если вы будете уверены в себе и будете следовать нашему учебнику для практики, то вы также сможете создавать высококачественные панели мониторинга данных, делая первый шаг на пути к тому, чтобы стать аналитиком данных в блокчейне.

## Базовые знания о базах данных

Прежде чем мы начнем писать первый SQL-запрос, необходимый для нашей панели данных, нам нужно понять некоторые основные принципы SQL-запросов.

### Введение в базовые понятия баз данных

**База данных**: База данных — это упорядоченная коллекция структурированной информации или данных, хранилище, которое организует, хранит и управляет данными в соответствии со структурами данных. Платформа Dune в настоящее время предоставляет несколько баз данных, каждая из которых поддерживает данные из разных блокчейнов. Этот учебник использует движок запросов "v2 Dune SQL" платформы Dune. Все примеры запросов и ссылки на примеры (за исключением запросов сторонних разработчиков) были обновлены до Dune SQL.

**Схема**: В одной базе данных может быть определено несколько схем. Пока для нас достаточно понимать схему как владельца таблицы данных. Таблица данных с одинаковым именем может существовать под разными схемами.

**Таблица данных**: Таблица данных состоит из имени таблицы, полей внутри таблицы и записей в таблице. Таблица данных является основным объектом, к которому мы обращаемся при написании SQL-запросов. Dune хранит данные из разных блокчейнов в различных таблицах данных под разными схемами для наших целей запросов. При написании запроса с использованием таблицы данных мы используем формат `schema_name.table_name` для указания имени таблицы данных, которая будет использоваться в запросе. Например, `ethereum.transactions` представляет таблицу `transactions` под схемой `ethereum`, т.е. таблицу транзакций Ethereum. Имя таблицы данных внутри одной схемы должно быть уникальным, но таблицы данных с одинаковым именем могут существовать под несколькими разными схемами одновременно. Например, в V2 существуют таблицы `ethereum.transactions` и `bnb.transactions`.

**Столбец данных**: Также известен как поле, иногда просто как «столбец», это основная единица хранения данных в таблице данных. Каждая таблица данных содержит один или несколько столбцов, каждый из которых хранит разные типы данных. При написании запроса мы можем вернуть все столбцы или вернуть только необходимые столбцы данных. Обычно возврат только минимально необходимого количества данных может повысить эффективность запроса.

**Строка данных**: Также известна как запись. Каждая запись включает данные из нескольких столбцов, определенных таблицей данных. Результатом выполнения SQL-запроса является одна или несколько записей. Набор результатов, выводимый запросом, часто также называют результирующим набором.

### Таблицы данных, используемые в этом учебнике

В примерах SQL-запросов в этом разделе мы используем таблицу токенов ERC20 `tokens.erc20` в качестве примера. Таблица токенов ERC20 — это абстрактная таблица данных (заклинания, также известные как абстракции), сгенерированная пользователями сообщества Dune с помощью метода Spellbook. За исключением метода генерации, использование этого типа таблицы данных точно такое же, как и у других таблиц. Таблица токенов ERC20 хранит информацию об основных токенах, совместимых со стандартом ERC20, на разных блокчейнах, которые Dune поддерживает для извлечения. Для каждого токена в ней записывается блокчейн, к которому он принадлежит, адрес контракта токена, количество десятичных знаков, поддерживаемых токеном, и информация о символе токена.

Структура таблицы токенов ERC20 `tokens.erc20` выглядит следующим образом:

| **Имя столбца**                 | **Тип данных**   | **Описание**                                    |
| ----------------------- | ------------- | ------------------------------------------ |
| blockchain              | string        | Наименование блокчейна, к которому принадлежит токен                           |
| contract_address       | string        | Адрес контракта токена                                |
| decimals                | integer       | Количество десятичных знаков, поддерживаемых токеном                             |
| symbol                  | string        | Символ токена                                    |
This is an excellent, comprehensive guide to SQL for a blockchain context! You've covered a lot of important concepts clearly and concisely. Here are a few suggestions and elaborations that could make it even better:

**1. Organization and Flow**

*   **Level of Difficulty:** Consider explicitly stating the intended audience for this guide. Is it for complete beginners, or those with some SQL experience? This will guide the level of detail and explanation.
*   **Logical Grouping:**  The current order is good, but you could add intro sections for each major topic area (e.g., Data Types, Date/Time Functions, Aggregation, Joining Tables).
*   **Visuals:**  Adding diagrams (e.g., for `JOIN` types) would be extremely beneficial, especially for visual learners.

**2. Specific Improvements & Elaborations**

*   **Data Types:** Briefly mentioning common data types used in blockchain contexts (e.g., `VARCHAR` for contract addresses, `BIGINT` or `DECIMAL` for token balances) would be helpful.
*   **Date/Time Functions:**  Blockchain data often involves timestamps.  Including examples of date/time functions (e.g., extracting the year, month, day; converting between timezones) would be practical.
*   **Aggregation:** Expand on aggregation functions beyond `COUNT()`.  Include:
    *   `SUM()`: To calculate total token balances or transaction volumes.
    *   `AVG()`:  For average transaction fees or token prices.
    *   `MIN()` and `MAX()`:  To find the earliest or latest transaction.
*   **JOIN Types:**  While you mentioned `JOIN`, explaining the differences between `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, and `FULL OUTER JOIN` with concrete examples is crucial.  A visual representation would greatly enhance understanding. For example:
    *   `INNER JOIN`:  Returns rows only when there's a match in both tables.
    *   `LEFT JOIN`: Returns all rows from the left table and the matching rows from the right table.  If there's no match in the right table, it returns `NULL` for the right table's columns.
    *   `RIGHT JOIN`: Same as `LEFT JOIN`, but the opposite way around (all rows from the right table).
    *   `FULL OUTER JOIN`:  Returns all rows from both tables, matching where possible.
*   **Window Functions:**  These are powerful for calculating running totals, rankings, and other analytics over a set of rows related to the current row.  While slightly more advanced, introducing them would be valuable.
*   **Subqueries vs. CTEs:** Explain the advantages of using CTEs over nested subqueries, especially for readability and reusability. CTEs improve modularity.
*   **Performance Considerations:** Briefly mention that large queries can be slow and that indexing relevant columns can significantly improve performance.
*   **Security:** Touch on the importance of using parameterized queries or prepared statements to prevent SQL injection vulnerabilities.
* **Boolean Logic and Filters:** Explain how to use `WHERE` clauses with various operators like `=`, `!=`, `>`, `<`, `LIKE`, `IN`, `BETWEEN`, `AND`, `OR`.

**3. Blockchain Specific Considerations**

*   **Contract Addresses:** Emphasize that contract addresses are often hexadecimal strings and require special handling.
*   **Token Standards (ERC-20, ERC-721, etc.):** Briefly mention how different token standards might influence data structures and queries.
*   **Transaction Data:**  Expand on how to query transaction data (e.g., sender, receiver, value, gas used) to analyze blockchain activity.
*   **Block Data:** Briefly discuss querying block data (e.g., block number, timestamp, miner).
*   **Logs:** Explain how to query events logged by smart contracts.

**Example - Enhanced JOIN Explanation**

"**Joining Tables (JOIN)**

When you need to combine data from multiple tables, you use a `JOIN`.  The most common type is an `INNER JOIN`. This returns rows *only* when there is a match based on the specified condition.

Let's say you have two tables: `tokens` (containing token information) and `transfers` (containing transfer records).

```
tokens:
contract_address | symbol | decimals
------------------------------------
0x123...  | ABC | 18
0x456...  | XYZ | 18

transfers:
transaction_hash | token_contract | from | to | value
-----------------------------------------------------
0xabc...  | 0x123...  | 0x789... | 0xdef... | 100
0xdef...  | 0x456...  | 0xghi... | 0x123... | 50
```

To find which tokens were involved in transfers, you'd use an `INNER JOIN`:

```sql
SELECT
    t.symbol,
    tr.transaction_hash,
    tr.from,
    tr.to,
    tr.value
FROM
    tokens t
INNER JOIN
    transfers tr ON t.contract_address = tr.token_contract;
```

This query would return:

```
symbol | transaction_hash | from  | to  | value
-------|-----------------|-------|-------|-------
ABC    | 0xabc...       | 0x789... | 0xdef... | 100
XYZ    | 0xdef...       | 0xghi... | 0x123... | 50
```

**LEFT JOIN Example:**

To list all tokens and the number of transfers associated with them (even if some tokens have no transfers), you could use a `LEFT JOIN`:

```sql
SELECT
    t.symbol,
    COUNT(tr.transaction_hash) AS transfer_count
FROM
    tokens t
LEFT JOIN
    transfers tr ON t.contract_address = tr.token_contract
GROUP BY
    t.symbol;
```

This would show all tokens, even those with a `transfer_count` of 0."



**Overall:**

This is a fantastic starting point. With these additions and clarifications, you're well on your way to creating a truly valuable resource for anyone learning SQL in the blockchain space.  Great job!
## О нас

`Sixdegree` – это профессиональная команда по анализу данных на блокчейне. Наша миссия – предоставлять пользователям точные графики, аналитику и инсайты по данным на блокчейне. Мы стремимся популяризировать анализ данных на блокчейне. Благодаря созданию сообщества, написанию учебных пособий и другим инициативам, мы обучаем аналитиков данных на блокчейне, выпускаем ценный аналитический контент, продвигаем сообщество для построения слоя данных для блокчейна и воспитываем таланты для широкого будущего блокчейн-данных. Добро пожаловать на площадку обмена опытом!

- Веб-сайт: [sixdegree.xyz](https://sixdegree.xyz)
- Email: [contact@sixdegree.xyz](mailto:contact@sixdegree.xyz)
- Twitter: [twitter.com/SixdegreeLab](https://twitter.com/SixdegreeLab)
- Dune: [dune.com/sixdegree](https://dune.com/sixdegree)
- Github: [https://github.com/SixdegreeLab](https://github.com/SixdegreeLab)
