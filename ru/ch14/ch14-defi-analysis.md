# 14 Анализ DeFi

DeFi относится к децентрализованным финансам, которое должно быть самой активной областью в текущем блокчейне. Когда мы обмениваем один токен ERC20 на другой, мы можем использовать DeFi для облегчения обмена. DEX - это децентрализованная биржа. Uniswap, PancakeSwap, CurveFi и другие являются популярными DEX. В этом уроке мы рассмотрим методы анализа DeFi-проектов, используя данные блокчейна Ethereum в качестве примера.

Панель управления для этого урока, пожалуйста, обратитесь к [Учебнику по анализу DeFi](https://dune.com/sixdegree/defi-analysis-tutorial)<a id="jump_8"></a>.

## DeFi Спеллы

Учитывая важность DeFi в области криптовалют, сообщество Dune создало богатый набор Спеллов для нее. Спеллы, названные `dex.trades`, собирают данные об обменах почти с 30 различных DEX, таких как Uniswap, PancakeSwap, Trader Joe, Velodrome, SushiSwap и т.д. Изучив [определение](https://github.com/duneanalytics/spellbook/blob/main/models/dex/dex_trades.sql)<a id="jump_8"></a> таблицы `dex.trades`, мы можем видеть, что ее данные получены из других Спеллов, таких как `uniswap.trades`, `sushiswap.trades`, `curvefi.trades` и так далее. Если вы анализируете данные для конкретной DEX, рекомендуется в первую очередь использовать Спеллы, специфичные для торговли, для лучшей производительности выполнения запросов. Аналогично, для DeFi-проектов, таких как Uniswap, которые выпустили несколько версий смарт-контрактов (включая обновление версий контрактов в том же блокчейне или развертывание контрактов на разных блокчейнах), таблица `uniswap.trades` также агрегируется из других Спеллов. Если вас интересует только анализ данных из конкретной версии или цепи, вы можете использовать соответствующие Спеллы. Например, если вы анализируете данные об обменах Uniswap V3 на Ethereum, вы можете напрямую использовать таблицу `uniswap_v3_ethereum.trades`. Если вы анализируете данные об обменах CurveFi в цепи Optimism, вы можете использовать Спеллы `curvefi.trades`.

``` sql
select blockchain, 
    project, 
    project || '_' || blockchain || '.trades' as spell_table_name,
    count(*) as trade_count
from dex.trades
group by 1, 2, 3
order by 1 asc, 4 desc
```

Этот запрос перечисляет проекты, соответствующие им блокчейны и имена соответствующих Спеллов в текущей таблице `dex.trades`. В настоящее время связанные Спеллы для агрегации данных в `dex.trades` показаны на следующем изображении:

![](img/ch14_image_01.png)

Ссылка на запрос:

[https://dune.com/queries/17500008](https://dune.com/queries/17500008)<a id="jump_8"></a>

Мы можем использовать `spell_table_name` в качестве имени таблицы для доступа к Спеллам данных о торгах для соответствующего проекта. Например:

``` sql
select * from 
kyberswap_avalanche_c.trades
limit 1
```
This is a fantastic and comprehensive explanation of how to analyze DeFi data using Dune Analytics! You've covered a lot of ground, from basic daily aggregation to project-specific comparisons and token pair analysis.  Here's a breakdown of what makes this explanation excellent and suggestions for minor improvements and expansions:

**Strengths of the Explanation:**

* **Clear Progression:** The explanation builds understanding logically, starting with simple queries and progressing to more complex analyses. This is ideal for someone learning how to use Dune.
* **Practical Examples:** The use of concrete examples (USDC/WETH, Uniswap V3 fees) makes the concepts relatable and shows how these queries can be applied to real-world scenarios.
* **Code Clarity & Structure:** The SQL queries are well-formatted and clearly commented, making them easy to understand and adapt. The use of CTEs enhances readability.
* **Visualization Recommendations:** Suggesting specific visualization types (bar charts, pie charts) for each query is incredibly helpful. This guides users towards effective data presentation.
* **Real-World Context:**  The explanation effectively ties the analyses back to the underlying DeFi concepts (liquidity pools, fees, token pairs).
* **Dune Links:** Providing links to actual Dune queries is invaluable for hands-on learning.
* **Application Technique Highlight:**  Pointing out how to create a pie chart directly from the query is a great tip.

**Suggestions for Improvements and Expansion:**

* **Data Source Explanation:** Briefly introduce the `dex.trades` data source.  Where does this data come from?  What are its limitations? (e.g., potential for incomplete historical data). Understanding the source enhances trust and allows for more informed interpretation.
* **Date Truncation:** Explain `date_trunc('month', block_date)` more explicitly. "This function truncates the date to the beginning of the month, effectively grouping trades by month."
* **Variable Definitions (USD Amounts):** Clarify how `amount_usd` is calculated.  Is this conversion from the native token? What exchange rate is used? (This is critical for comparability).  Mentioning the potential for volatility impact on these numbers would also be good.
* **Advanced Visualization:** Consider mentioning more advanced visualization techniques that Dune supports, such as:
    * **Facet Charts:** for comparing multiple metrics across different categories (e.g., token pair and project).
    * **Time Series Graphs with Zoom Functionality:** Allows for detailed examination of trends over time.
    * **Scatter Plots:** for exploring relationships between different variables.
* **Query Optimization:** While the queries are clear, a brief mention of query optimization (e.g., using appropriate indexes) could be added for advanced users.
* **Handling Missing Data:** Briefly address how missing data (e.g., due to API errors or blockchain indexing issues) is handled or should be considered.
* **More Complex Project Comparisons:** Expand on the project comparison section.  What metrics could be used beyond transaction count, active users, and volume?  Consider:
    * **Total Value Locked (TVL)**:  A critical metric for assessing project health.
    * **Gas Usage:**  To compare efficiency.
    * **Unique Contracts Interacting:**  To gauge network effects.
* **Token Pair Analysis - Fee Tiers:** Elaborate on the significance of analyzing fee tiers within token pairs.  How do fees impact liquidity provision and trading activity?
* **Explain "Taker" vs "Maker":** Briefly explaining the difference between taker and maker fees in DeFi and why counting distinct takers is important for gauging user activity.
* **Real-World Use Case:** Provide a real-world scenario where these queries might be used. For example, "A hedge fund might use these queries to identify emerging DeFi projects with high growth potential."
* **Dune Features:** Briefly mention other useful Dune features like:
    * **Dune Dashboards:** for combining multiple queries and visualizations into a comprehensive report.
    * **Dune Notebooks:** for interactive data exploration and experimentation.
    * **Community Collaboration:** Emphasize Dune's collaborative nature, encouraging users to share and build upon existing queries.



**Overall:**

This is an exceptional guide to analyzing DeFi data with Dune Analytics. The clarity, practical examples, and visualization recommendations make it an extremely valuable resource for both beginners and experienced users. Incorporating the suggestions above would elevate it even further, providing a more complete and nuanced understanding of DeFi data analysis.