## Быстрый старт

## Введение

Наш учебник в значительной степени ориентирован на практическое применение и написан в соответствии со сценариями и потребностями ежедневного анализа данных Onchain. В этой статье будут объяснены основы SQL, которые вам необходимо понять, прежде чем начать создавать панели мониторинга данных. Этот учебник предназначен для начинающих и в первую очередь рассчитан на новых пользователей, которые хотят изучить анализ данных. Мы предполагаем, что у вас нет опыта написания SQL-запросов. Пользователи, обладающие опытом работы с SQL, но не знакомые с платформой Dune, также могут быстро просмотреть этот учебник. В этом учебнике в основном содержится введение в платформу Dune, быстрый старт для SQL-запросов и многое другое. В следующем учебнике мы будем писать запросы вместе, создавать визуальные диаграммы и использовать эти диаграммы для создания панелей мониторинга данных. Мы верим, что при наличии уверенности и следовании нашему учебнику для практики, вы также сможете создавать высококачественные панели мониторинга данных, сделав первый шаг к становлению аналитиком данных Onchain.
## Введение в платформу Dune

[Dune](https://dune.com/) — это мощная платформа для анализа данных блокчейна, которая предоставляет необработанные данные блокчейна и обработанные данные в виде SQL-базы данных. Используя SQL-запросы, мы можем быстро искать и извлекать различную информацию блокчейна из базы данных Dune, а затем преобразовывать ее в интуитивно понятные визуальные графики для получения информации.
Панель данных на Dune состоит из различных виджетов. Эти виджеты могут быть визуальными графиками или текстовыми полями, созданными на основе результатов запросов, и вы также можете встраивать изображения, ссылки и т. д. в текстовое поле.
Запрос является основным источником данных для панели данных Dune. Мы пишем SQL-выражения, выполняем запросы, генерируем визуальные графики на основе результирующего набора и затем добавляем графики в соответствующую панель данных.

Общий процесс использования Dune для обработки данных можно суммировать следующим образом: Написать SQL-запросы для отображения данных -> Визуализировать результаты запросов -> Собрать визуальные графики в панели данных -> Настраивать и оформлять панель данных. Для использования платформы Dune вы можете ознакомиться с ее [официальной документацией](https://dune.com/docs/).

This is a *fantastic* and comprehensive guide to SQL for blockchain data analysis.  You've hit all the crucial topics, and the explanations are clear and well-structured. Here's a breakdown of what's excellent and a few suggestions for improvement, primarily focused on making it *even more* beginner-friendly.

**Strengths:**

* **Clear Explanations:** The explanations for each concept are easy to understand, even for someone with limited SQL experience. The examples provided are concrete and help illustrate the points.
* **Relevant Examples:** Using the `tokens.erc20` table as a recurring example is brilliant. It provides context and reinforces the concepts. The examples are directly applicable to blockchain data.
* **Comprehensive Coverage:** You've covered a wide range of SQL techniques: date functions, string manipulation, calculations, joins, unions, case statements, and CTEs.
* **Rationale for `UNION ALL`:**  Explaining *why* `UNION ALL` is preferred for blockchain data is a crucial point for performance.
* **Structure & Organization:** The guide is well-organized and logical, making it easy to follow.
* **Emphasis on Practicality:** The focus is on *how* to use these techniques to analyze blockchain data, not just abstract SQL theory.

**Suggestions for Improvement (Primarily for Absolute Beginners):**

1. **Very Basic SQL Introduction:**
   * **What *is* SQL?**  Start with a sentence or two explaining the purpose of SQL: "SQL (Structured Query Language) is the standard language for interacting with databases. It allows you to retrieve, insert, update, and delete data."
   * **Databases and Tables:** Briefly explain the relationship between a database and tables.  "A database is a structured collection of data, and a table is a collection of data organized into rows and columns."
   * **`SELECT`, `FROM`, `WHERE`:** Before jumping into the more advanced functions, briefly introduce the core components of a `SELECT` statement:
      * `SELECT`:  Specifies the columns you want to retrieve.
      * `FROM`: Specifies the table you're retrieving data from.
      * `WHERE`: Specifies the conditions that must be met for a row to be included in the result set.  Show a very simple example:
          ```sql
          SELECT symbol, decimals
          FROM tokens.erc20
          WHERE blockchain = 'ethereum';
          ```

2. **Expand on Data Types (briefly):**  Mentions that fields can store text, numbers or dates would be a quick intro.

3. **Explain `ORDER BY`:**  After showing `SELECT`, `FROM`, `WHERE`, include an example using `ORDER BY` to show how to sort results.

4. **More Comments in Examples:** Add a few more comments to the SQL code examples to explain what each line is doing. While the text explanations are good, some inline comments can be helpful for those who want to copy and paste the code.

5. **Explain `AS` Alias:** When using aliases, briefly explain what the `AS` keyword does: "The `AS` keyword is used to give a column or table a temporary name."

6. **Case Statement - Alternative Syntax:**  Show the `CASE` statement using both the `THEN` and `ELSE` clauses, and also showing what happens when `ELSE` is omitted (it defaults to `NULL`).

7. **More Diverse Examples:** While the `tokens.erc20` table is excellent, showing a couple of examples with different table structures would broaden the applicability. Perhaps a table tracking transactions, or a table of user accounts.

8. **Clarify "Data Dashboard" Reference:** You mention "Query 6" in the "data dashboard" section. It's great if this is a follow-up document, but for a standalone guide, consider either including those queries directly or explaining the context.

**Illustrative Examples Incorporating Suggestions:**

Here's a modified intro and a revised `CASE` statement example incorporating some of these suggestions:

**Modified Intro (New):**

"SQL (Structured Query Language) is the standard language for interacting with databases. It allows you to retrieve, insert, update, and delete data. A database is a structured collection of data, and a table is a collection of data organized into rows and columns. Each column has a specific type like text, number or date. Let's start with a simple example:"

```sql
SELECT symbol, decimals
FROM tokens.erc20
WHERE blockchain = 'ethereum';  -- This retrieves the symbol and decimals from the tokens.erc20 table for tokens on the Ethereum blockchain.
```

"In this query:
* `SELECT` specifies that we want to see the `symbol` and `decimals` columns.
* `FROM` tells the database we are getting this data from the `tokens.erc20` table.
* `WHERE` filters the data to only include rows where the `blockchain` column is equal to 'ethereum'."

**Revised `CASE` Statement Example:**

"The `CASE` statement allows you to add conditional logic to your queries. Here's an example showing how to categorize tokens based on their number of decimals:"

```sql
SELECT
    symbol,
    decimals,
    CASE
        WHEN decimals = 6 THEN 'Standard'
        WHEN decimals = 9 THEN 'Stablecoin'
        WHEN decimals = 18 THEN 'ERC-20 Token'
        ELSE 'Other'  -- If the decimals don't match any of the conditions, the category will be 'Other'
    END AS token_category  -- We're giving this calculated column the name 'token_category'
FROM tokens.erc20;
```

"If the `ELSE` clause is omitted, then the categories for tokens where `decimals` doesn't match any of the `WHEN` conditions will be NULL."



By incorporating these small adjustments, you can make your already excellent guide even more accessible and welcoming to those who are completely new to SQL.  Well done!
