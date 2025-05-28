# 05 SQL Basics (I)

## Базовые понятия

### 1. Что такое хранилище данных?

Если говорить просто, то хранилище данных — это структурированное хранилище данных для статистических целей. Носителем хранения являются [**таблицы данных**]. Серия [**таблиц данных**], объединенных для одной или нескольких тем, называется хранилищем данных. Обратите внимание: данные здесь могут быть данными результатов (например, ежедневный объем торгов торговой пары на Uniswap с момента ее запуска). Это также могут быть данные процесса (каждая запись о транзакции торговой пары на Uniswap с момента запуска: кто инициировал ее, торговля A на B, время транзакции, tx_hash, сумма и т.д.).

**2. Что такое SQL?**

Предположим, вам нужны хрустящие шоколадные батончики Nestle, но вы не можете выйти прямо сейчас. Поэтому вы просите кого-нибудь сделать поручение: «Мне нужна коробка шоколадных батончиков, бренд Nestle». Порученец идет в супермаркет, покупает шоколад и доставляет его к вам домой.
Аналогично, SQL — это как фраза, которую вы произнесли, Dune Analytics — это порученец. Это позволяет вам общаться с хранилищем данных и извлекать из него данные. Самая базовая структура или синтаксис SQL имеет 3 компонента, почти все SQL будет содержать эти 3 части:

**select**: Какие поля извлекать?

**from**: Из какой таблицы извлекать?

**where**: Каковы критерии?

**3. Как выглядит таблица данных?**

Вы можете думать о таблице как об электронной таблице Excel, где каждый лист содержит разные данные. Возьмем `ethereum.transactions` (записи о транзакциях Ethereum) в качестве примера:

![](img/ch05_query-page.png)

В таблицах есть некоторые часто используемые поля:

- **block_time**: Временная метка, когда транзакция была добыта
- **block_number**: Номер блока, где была добыта транзакция
- **value**: Сумма ETH, переданного (нужно разделить на power(10,18) для десятичной точности)
- **from**: Адрес кошелька, с которого был отправлен ETH
- **to**: Адрес кошелька, на который был отправлен ETH
- **hash**: Хэш транзакции этой транзакции
- **success**: Успешно ли выполнена транзакция

Okay, this is a very thorough explanation of several SQL concepts and queries, using Dune Analytics examples. Here's a breakdown of what's being presented, with key takeaways and possible areas for improvement in the explanation itself.

**1. Core Concepts Covered**

*   **`date_trunc()`:** This function is used to truncate dates to a specific level of precision (e.g., `date_trunc('day', timestamp)` gives you the date without the time).
*   **`with as` (Common Table Expressions - CTEs):**  Creates named, temporary result sets that can be referenced within a single query.  This improves readability and allows for complex logic to be broken down into smaller, manageable steps.
*   **`join` and `left join`:**  Ways to combine data from two or more tables.
    *   `join` returns only rows where there's a match in both tables based on the join condition.
    *   `left join` returns all rows from the left table and matching rows from the right table.  If there's no match, the columns from the right table will be filled with `NULL`.
*   **Aggregation (`sum()`):**  Calculates the sum of values within a group.
*   **Grouping (`group by`):**  Groups rows with the same value in one or more columns, allowing for calculations (like `sum()`) to be performed on each group.
*   **Nested Queries (Subqueries):**  Queries embedded within other queries.  While useful, they can become complex and hard to read, which the use of `with as` helps to mitigate.

**2.  Detailed Explanation of the Queries**

Let's analyze each query and its purpose as presented.

*   **Query 1 (Daily ETH Amount):**  Calculates the total ETH amount transferred daily by a specific address.
*   **Query 2 (Detailed Data - USD Value):**  Calculates the USD value of each ETH transaction, using minute-level pricing.
*   **Query 3 (Aggregate Data - USD Value):**  Aggregates the detailed USD value transactions from Query 2 by day.  This simplifies the output to show the total daily value transferred in USD.

**3. Strengths of the Explanation**

*   **Clear Purpose:** Each query has a stated purpose.
*   **Step-by-Step:** The explanation clearly outlines the function of each SQL clause.
*   **Practical Examples:** The use of Dune Analytics queries provides a real-world context.
*   **Comparison of `join` and `left join`:** The differentiation between these two join types is explained well.
*   **Highlighting `with as`:**  The explanation effectively demonstrates how `with as` improves readability and modularity.

**4. Potential Areas for Improvement in the Explanation**

*   **More Precise Terminology:** While the explanation is generally good, some phrasing could be more precise. For example:
    *   Instead of "virtual table," it's more accurate to call it a **Common Table Expression (CTE)**.  Mentioning CTE explicitly would be beneficial.
    *   The description of `join` could clarify that it's an *inner join* (explicitly stating it's an inner join, because it's the default when just writing `join` is better.
*   **Explain the Data Model:** A brief description of the underlying data model (what tables exist and what columns they contain) would be extremely helpful, especially for someone new to the data. What are the columns in `ethereum.transactions`? What does the `prices.usd` table contain?
*   **Expand on the Benefits of CTEs:**  Beyond just readability, CTEs can also be useful for recursion or when the same subquery is used multiple times within a single query, preventing repetition.
*   **Consider the Audience:**  Who is this explanation for? A beginner? An intermediate user? Tailor the language and level of detail accordingly. For beginners, more foundational SQL concepts might need to be introduced.
*   **Show the Raw Data:**  Including snippets of the raw data before and after each query would enhance understanding.  "Here's the data *before* the query... Here's the data *after* the query..."
*   **Address Potential Issues:** Are there edge cases or limitations in these queries (e.g., missing price data for certain minutes)? Briefly mentioning these would show a more complete understanding.



**5.  Overall Assessment**

This is a very good and useful explanation of SQL concepts and how they apply to data analysis. The use of Dune Analytics examples makes it practical and relatable. Addressing the minor improvements listed above would elevate the explanation even further and make it accessible to a wider range of users.