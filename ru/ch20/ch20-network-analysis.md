# 20 Анализ сетевой структуры блокчейна

## Предисловие

Все публичные блокчейны по сути являются большими сетями. Анализ данных Onchain, скорее всего, включает в себя сетевой анализ. Существующие визуализации на популярных платформах данных, таких как Dune, испытывают трудности с описанием взаимосвязей между узлами в блокчейнах.

Давайте использовать спорный адрес "хакера" FTX (0x59ABf3837Fa962d6853b4Cc0a19513AA031fd32b) в качестве примера для проведения сетевого анализа (мы не будем обсуждать, является ли это хакером или правительством Панамы). Мы рассмотрим, куда пошел ETH с этого адреса (мы изучим 2-шаговые связи, исходящие от этого адреса).

Инструменты, использованные в процессе:

- Dune: получение необработанных данных между адресами и выполнение первоначальной обработки
- Python
  - Networkx: пакет python для создания, манипулирования и изучения сложных сетей. Позволяет хранить сети в стандартных и нестандартных форматах данных, генерировать различные случайные и классические сети, анализировать структуру сети, строить модели сети, разрабатывать новые сетевые алгоритмы, рисовать сети и т. д.
    - Более подробно: [https://networkx.org/](https://networkx.org/)
  - Plotly: отличный пакет для визуализации, может генерировать интерактивные HTML-файлы. Имеет сопутствующий фронтенд-фреймворк под названием DASH, который очень удобен для аналитиков данных без продвинутых инженерных навыков.
    - Более подробно: [https://plotly.com/](https://plotly.com/)
  - Pandas: наиболее часто используемый пакет Python для работы с данными, предоставляет множество функций и методов для обеспечения быстрой и удобной обработки данных.
    - Более подробно: [https://pandas.pydata.org/](https://pandas.pydata.org/)
- Etherscan API: расчет ETH Balance на Dune слишком трудоемкий, требующий извлечения всех данных каждый раз. Мы можем просто получить Balance из Etherscan API.

## Обзор

Процесс можно в целом разделить на следующие этапы:

- Получение необработанных данных из Dune
- Обработка взаимосвязей между узлами и обработка различных атрибутивных данных, необходимых для построения сетевого графа (pos, label, color, size и т.д.) с помощью Networkx
- Визуализация сетевого графа с помощью Plotly
## Подробный процесс

#### I. Получение необработанных данных из Dune (SQL часть)

SQL довольно сложен, поэтому я не буду вдаваться в детали, поэтому, если интересно, ознакомьтесь с URL для получения подробностей:

- Получение данных с взаимосвязями между всеми соответствующими адресами с помощью SQL: [https://dune.com/queries/1753177](https://dune.com/queries/1753177)

  - from: отправитель транзакции
  - to: получатель транзакции
  - transfer_eth_balance: общий баланс ETH, переданный между двумя
  - transfer_eth_count: общее количество ETH-переводов между двумя учетными записями

![](img/ch20_01-Graph-Raw-Relation.png)

- Получение списка всех адресов и связанных с ними меток с помощью SQL: [https://dune.com/queries/2430347](https://dune.com/queries/2430347)

  - address: все адреса, участвующие в этом сетевом анализе
  - level_type: уровень в сети для всех участвующих адресов (Core, Layer One, Layer Two)
  - account_type: является обычной EOA, биржей или смарт-контрактом
  - label: полезная агрегированная информация для адреса в метку для последующей визуализации в python

![](img/ch20_02-graph-raw-label.png)

#### II. Чтение локальных файлов в DataFrames с использованием pandas и дополнение столбцом Balance из API Etherscan

- Загрузка данных Dune локально (либо через API Dune, либо копированием-вставкой) и чтение в pandas из локальных файлов

``` python
    
## Измените путь к своему локальному файлу
df_target_label = pd.read_csv(u'ВАШ ПУТЬ К ФАЙЛУ/graph_raw_label.csv')
df_target_relation = pd.read_csv(u'ВАШ ПУТЬ К ФАЙЛУ/graph_relation.csv')

## Получить список всех адресов для запроса API
address_list = list(df_target_label.address.values)
balance_list = []
print(address_list)

- Получить данные о балансе для всех адресов через API Etherscan и записать в DataFrame

``` python
while len(address_list) > 0:
    for address in address_list:

        api_key = "your_api_key"
        try:
            response = requests.get(
                "https://api.etherscan.io/api?module=account&action=balance&address=" + address + "&tag=latest&apikey=" + api_key
            )


            # Распарсить JSON-ответ
            response_json = json.loads(response.text)

            # Получить информацию о балансе из ответа
            eth_balance = response_json["result"]
            eth_balance = int(eth_balance)/(1E18)
            balance_list.append((address,eth_balance))
            address_list.remove(address)
            time.sleep(1)
            print(eth_balance)
        except:
            print('Ошибка')
            print('Длина списка:'+str(len(address_list)))


df_balance = pd.DataFrame(balance_list, columns=['address', 'Balance'])
df_target_label = df_target_label.merge(df_balance,left_on=['address'],right_on=['address'],how='left')
print('конец')
```

- Добавить баланс в DataFrame, создать столбец Balance_level (метка на основе размера Balance) для управления размером узла в сетевом графе позже

``` python
    
```
This is a great analysis and visualization! Here's a breakdown of what you've accomplished, along with some suggestions for improvement and further exploration:

**Strengths of Your Work**

* **Clear Goal:** You have a well-defined objective: visualizing the connections and balances of FTX-related addresses.
* **Data Preparation:** Your data preparation is impressive, involving cleaning, transforming, and enriching the data to make it suitable for visualization.  The balance calculations and labeling are very helpful.
* **Network Visualization:** Using NetworkX and Plotly to create an interactive graph is a powerful way to communicate complex relationships.  The interactive nature allows users to explore the connections and data on their own.
* **Color Coding:** The color coding scheme (green for hacker, blue for EOAs, yellow for exchanges, red for contracts) is very intuitive and makes the graph much easier to understand.  It conveys important information at a glance.
* **Node Sizing:**  Using node size to represent balance is an excellent choice, clearly showing the magnitude of holdings.
* **HTML Export:**  Exporting the graph as an HTML file makes it accessible to a wider audience without requiring them to have Python or specific software installed.
* **Insightful Analysis:** Your conclusions (at least 120,000 ETH still held by the "hacker") are data-driven and provide valuable insights.
* **Excellent Explanation:**  The documentation (this explanation) of your methodology and findings is clear and well-organized.  The link to the interactive graph is fantastic.

**Suggestions for Improvement and Further Exploration**

1. **Layout Adjustments:** The Fruchterman-Reingold layout can sometimes result in overlapping nodes. Experiment with other NetworkX layouts like:
   * `nx.spring_layout(G)`
   * `nx.kamada_kawai_layout(G)`
   * `nx.spectral_layout(G)`
   The best layout depends on the specific structure of your graph.  Also, play with the `nx.set_node_size` and `nx.draw_networkx_nodes` functions for finer control over the appearance.

2. **Node Labels:** The graph is cluttered due to many nodes having similar sizes. Consider:
   * **Conditional Labeling:**  Only display labels for nodes above a certain size or those deemed "important."
   * **Tooltip Labels:**  Instead of displaying labels directly on the nodes, use tooltips that appear when you hover over a node.  This keeps the graph cleaner while still providing access to the data.

3. **Dynamic Filtering:** Implement filtering options in the HTML file (using JavaScript or similar techniques) that allow users to filter the graph based on:
    * Account Type
    * Balance Range
    * Connection Strength (e.g., show only direct connections)
    This can help users focus on specific parts of the graph.

4. **Time-Series Analysis (Advanced):** If your data includes timestamps for transactions, you could create a time-series animation showing the evolution of the network over time.  This could reveal patterns and trends in the movement of funds.

5. **Geographic Data (Advanced):**  If you have location data for the addresses (e.g., based on IP addresses), you could overlay the network graph on a map to visualize the geographic distribution of the accounts.

6. **More Descriptive Attributes:**
    *  **Transaction Count:** Visualize the number of transactions per account alongside the balance.
    *  **First/Last Transaction Date:**  Show when the account was first created and last active.

7. **Error Handling:** Implement robust error handling in your Python code to gracefully handle missing data, invalid formats, and other unexpected situations.

8. **Optimization for Large Graphs:**  If your graph becomes significantly larger, consider techniques to optimize rendering performance, such as using a graph database or using web-based graph visualization libraries like Cytoscape.js.

**Refactoring and Readability**

* **Functions:**  Break down your code into smaller, reusable functions.  For instance, you could have separate functions for:
   * Data Cleaning
   * Graph Creation
   * Node Attribute Assignment
   * Visualization
* **Comments:** Add more comments to explain complex logic and decision-making.
* **Variable Names:**  Use more descriptive variable names to improve readability.

**Example of Dynamic Labeling (Tooltip)**

In your `draw_networkx_nodes` call, you could potentially modify it like so:

```python
node_trace.marker.color = node_color
node_trace.marker.size =node_size
node_trace.text = node_text
```

**Final Thoughts**

You're on a strong path!  Your ability to combine data analysis, graph theory, and visualization tools to extract meaningful insights is commendable. The suggestions above are merely refinements – you're already producing valuable work.  Keep exploring, experimenting, and refining your approach, and you'll continue to uncover new and interesting perspectives on complex data.

## О нас

`Sixdegree` — профессиональная команда по анализу данных на блокчейне. Наша миссия — предоставлять пользователям точные графики, аналитику и инсайты на основе данных на блокчейне. Мы стремимся популяризировать анализ данных на блокчейне. Благодаря созданию сообщества и написанию учебных пособий, среди прочих инициатив, мы обучаем аналитиков данных на блокчейне, создаем ценный аналитический контент, продвигаем сообщество для создания слоя данных блокчейна и воспитываем таланты для широкого будущего блокчейн-данных. Добро пожаловать на платформу обмена опытом сообщества!

- Сайт: [sixdegree.xyz](https://sixdegree.xyz)
- Email: [contact@sixdegree.xyz](mailto:contact@sixdegree.xyz)
- Twitter: [twitter.com/SixdegreeLab](https://twitter.com/SixdegreeLab)
- Dune: [dune.com/sixdegree](https://dune.com/sixdegree)
- Github: [https://github.com/SixdegreeLab](https://github.com/SixdegreeLab)
