# 00 Становление аналитиком данных на блокчейне #

**TLDR**

- Богатство данных на блокчейне проистекает из зрелости технологии блокчейн и инноваций проектов.
- Освоение перспективы анализа данных на блокчейне помогает уменьшить информационную асимметрию и добавляет дополнительный уровень защиты при навигации по темному лесу.
- Данные на блокчейне действительно отражают поток ценности, поэтому полученные из них выводы более ценны.
- Анализ данных предоставляет количественную перспективу для поддержки принятия решений; анализ — это процесс, а не конечная цель.
- Хороший анализ данных исходит от анализа данных, способности к абстрагированию и требует углубления понимания отрасли.

## Что такое данные на блокчейне
Большинство людей, когда впервые знакомятся с блокчейном, понимают следующее: блокчейн — это публичный, защищенный от изменений реестр, и все переводы, а также записи транзакций являются прозрачными и заслуживающими доверия. Однако это не единственная функция блокчейна. Это всего лишь начальная точка отхода от «системы электронных денег, работающих напрямую между участниками» — то есть, «реестра». С развитием смарт-контрактов блокчейн фактически становится большой базой данных. Следующая схема сравнивает архитектуру традиционных web2 и web3 приложений: смарт-контракты заменяют бэкэнд, а блокчейн также берет на себя некоторые функции базы данных. Появляется все больше проектов на блокчейне, и наши взаимодействия на блокчейне становятся все более частыми, например, сколько ликвидности мы добавили в DeFi протоколы, какие NFT мы отчеканили и даже какие социальные аккаунты мы отслеживаем, могут быть записаны на блокчейне. Все наши взаимодействия с блокчейном будут записаны в этой базе данных, и эти записи будут относиться к данным на блокчейне.

[Изображение img/ch00_01.png]

**Данные на блокчейне приблизительно делятся на три категории:**

1. Данные транзакций.
Такие как адреса отправителя и получателя, сумма перевода, баланс адреса и т. д.

2. Данные блоков.
Например, метки времени, комиссия майнера, вознаграждение майнера и т. д.

3. Код смарт-контракта.
То есть бизнес-логика, закодированная на блокчейне.

Анализ данных на блокчейне — это извлечение необходимой информации из этих трех типов данных для интерпретации.
С точки зрения стека данных, продукты данных о блокчейне можно разделить на источники данных, инструменты разработки данных и приложения для данных.

[Изображение img/ch00_02.jpg]

Гибкое использование различных продуктов данных предоставит нам новый взгляд на мир криптовалют.

Хотя мы всегда говорим, что данные на блокчейне являются публичными и прозрачными, нам трудно напрямую читать эти данные, потому что простой своп-транзакция на блокчейне может выглядеть следующим образом:

[Изображение img/ch00_03.png]

Мы можем видеть некоторые необработанные данные на блокчейне в блокчейн-обозревателе, но если мой вопрос в том, какой объем торгов UniswapV3 сегодня, это не решает мою проблему!

[Изображение img/ch00_04.png]

Исходные данные на блокчейне не дают нам ответа, поэтому нам нужно пройти серию процессов поглощения данных, таких как индексация, обработка, хранение, а затем агрегировать соответствующие данные в соответствии с поднятым вопросом, чтобы найти ответ.

[Изображение img/ch00_data-process.png]

Чтобы начать с нуля, нам, возможно, придется настроить собственные узлы для получения данных о блокчейне и затем обрабатывать их, но это, очевидно, очень трудоемко и затратно. К счастью, существуют платформы данных, такие как Dune, Flipside, Footprint, которые после обработки принимают исходные данные на блокчейне, полученные с помощью индексации, и хранят их в хранилище данных, управляемом и обновляемом соответствующей платформой. Другими словами, эти платформы превращают все данные блокчейн во множество реляционных таблиц данных. Нам нужно делать это: выбирать некоторые данные, которые мы хотим, из таблицы для построения наших аналитических данных. Кроме того, продукты данных, такие как Nansen, Messari и DeBank, не только организуют данные, но и упаковывают их в соответствии с потребностями пользователя, чтобы сделать их удобными для прямого использования.

|Категория | Пример применения|
|--------|:---------------:|
|Приложение данных | Nansen, Messari, DeBank..|
|Платформа данных |Dune, FLipside, Footprint.. |
|Узел данных | Infura, Alchemy, Blockdaemon|


## Важность данных в блокчейне
С процветанием экосистемы в блокчейне появилось огромное количество данных благодаря богатому интерактивному поведению.
Эти данные в блокчейне соответствуют потоку ценности в блокчейне, и, как следствие, выводы, полученные в результате анализа, становятся чрезвычайно ценными. Благодаря прозрачным и правдивым данным в блокчейне мы можем делать более выгодные решения, выявляя психологическое состояние и ожидания трейдеров, и даже всего рынка. Они также могут послужить маяком света в темном лесу, освещая путь вперед для нашей защиты.

Возьмем знакомый протокол DeFi ликвидного майнинга в качестве примера: вы добавляете ликвидность, чтобы получить вознаграждение, глубина пула увеличивается, пользователи получают более низкий проскальзывание, у всех светлое будущее, и вы безопасно блокируете свои деньги в контракте. Однажды тихо появляется черный лебедь, "умные" деньги с инсайдерской информацией немедленно отступают, и вы — просто обычный инвестор; только когда вы видите негативные новости и думаете об изъятии, вознаграждения в ваших руках почти ничего не стоят, и серьезные непостоянные потери затрудняют выход в безубыток.

[Изображение img/ch00_scam.png]

Но если у вас есть перспектива данных в блокчейне, вы можете обнаружить: TVL протокола внезапно падает, а объем продажи токена на Uniswap резко возрастает. Другими словами, умные люди получают новости или находят что-то не так, ликвидность в пуле становится хуже, и деньги утекают, и все настроены пессимистично в отношении токена и продают его как сумасшедшие — стоить ли мне сейчас выходить?

Конечно, это всего лишь абстрактный и простой пример, но то, что я хочу вам передать: обычные инвесторы в крипто-лесном мраке всегда находятся в невыгодном положении с точки зрения информационного неравенства. Но данные в блокчейне прозрачны и правдивы. Почему все так одержимы отслеживанием Nansen Smart Money? Потому что люди с инсайдерской информацией не скажут вам новости, но информация будет отражена в поведении в блокчейне и записана правдиво. Нам остается только внимательно наблюдать за этим миром данных, улавливая детали в блокчейне, чтобы в некоторой степени восполнить разрыв в информации.

После DeFi лета мы начали заботиться об TVL протоколов; Axie взорвался, мы изучали ежедневное увеличение числа пользователей; NFT взлетели, мы изучали количество чеканов; Gas в Ethereum взлетел, мы наблюдали, какой проект был таким горячим. Заметили ли вы? Наше растущее понимание и чувствительность к данным в блокчейне на самом деле происходят из процветающего развития деятельности в блокчейне. Другими словами, важность данных в блокчейне происходит от зрелости технологии блокчейн и бума приложений. Все больше проектов в блокчейне дают нам достаточно богатого пространства для взаимодействия, в то же время, благодаря зрелости и широкому применению SBT и OAT. Все, что происходит в блокчейне, становится возможным, что означает, что в будущем данных будет достаточно для поддержки полного блокчейн-портрета каждого пользователя. К тому времени мы сможем лучше рассказывать истории о DID, SocialFi.

## Кто Будет Заниматься Анализом Данных в Цепочке

Для большинства пользователей готовых продуктов с данными достаточно,
и хороший эффект можно достичь, гибко комбинируя различные инструменты для работы с данными.
Например, используя Nansen, чтобы помочь пользователям отслеживать движения крупных игроков в реальном времени;
используя Token Terminal, чтобы просматривать доход различных протоколов;
платформы мониторинга данных NFT еще более разнообразны.
Эти «готовые» продукты с данными, хотя и отличаются низким порогом входа и простотой использования,
также имеют узкое место, которое не может удовлетворить высокие требования к индивидуализации.

[Изображение img/ch00_07.jpg]

Например, вы через https://ultrasound.money/
обнаруживаете, что потребление газа в Ethereum внезапно возрастает,
движимое этим XEN, о котором вы никогда не слышали.
Вы проницательно осознаете, что это может быть ранняя возможность!
Через поиск в Twitter вы узнаете, что XEN использует механизм майнинга PoP (Proof of Participation).
Участники майнинга XEN владеют всеми добытыми ими токенами XEN.
По мере увеличения количества участников возрастает сложность майнинга и уменьшается предложение. Вы хотите понять ситуацию с участием каждого,
поскольку полагаться только на потребление газа недостаточно; вам также нужно знать количество участников, тенденции и как долго участники выбирают для блокировки.
В то же время вы также обнаруживаете, что, по-видимому, отсутствует защита от сибиллов.
Оплата газа для участия - сколько ученых бросаются? Останутся ли у меня какие-либо прибыли?
Когда вы анализируете это, вам срочно нужны данные для поддержки вашего решения «идти или не идти»;
но поскольку это ранняя стадия, в приложении для анализа данных нет анализа этого, и приложение также вряд ли будет отслеживать и анализировать каждый протокол.
Вот почему, несмотря на существование множества продуктов с данными, нам все же необходимо уметь самостоятельно писать некоторые анализы данных: существующие продукты не могут удовлетворить индивидуальные потребности.

[Изображение img/ch00_xen.png]

С помощью собственного анализа данных https://dune.com/sixdegree/xen-crypto-overview,
я выяснил, что большинство людей выбирают краткосрочные залоги, и около 70% - это новые кошельки, что указывает на то, что они были использованы всеми.
Поэтому я понимаю, что давление краткосрочной продажи будет очень большим; если я решу поучаствовать, я выберу кратчайшее время залога и продам как можно скорее, чтобы увидеть, кто побежит быстрее.
В этот момент вы завершили весь процесс анализа данных в цепочке: обнаружили проект, изучили механизм проекта, вывели стандарт оценки проекта и, наконец, - выполнили обработку данных, визуализацию и поддержку принятия решений.

## Как проводить анализ данных в блокчейне

Хотя платформы анализа данных, такие как Dune, проделали большую подготовительную работу для нас, нам просто нужно использовать синтаксис, похожий на SQL, чтобы извлекать необходимые фрагменты из таблицы данных для построения. У большинства людей путь обучения, как я думаю, заключается в том, чтобы спешить к "3 дням, чтобы освоить SQL"; после этого они снова чувствуют себя потерянными и все еще не знают, как найти нить в клубке шерсти. Почему так происходит? Самое важное в анализе данных — это развитие мышления, ориентированного на данные, а владение языками программирования вторично.

Анализ данных предоставляет количественную перспективу для поддержки принятия решений — анализ — это процесс, а не конечная цель. Простые шаги — это уточнение трех вопросов и развитие мышления, ориентированного на данные:

**1. Какова моя цель?**

Речь идет о том, чтобы определить, подходящее ли время для покупки монеты сейчас?
Решить, добавлять ли ликвидность в AAVE для получения дохода?
Или узнать, не поздно ли сейчас вступать в Stepn?

**2. Какова моя стратегия?**

Стратегия покупки монет заключается в том, чтобы следовать за "умными деньгами", покупать то, что они покупают, входить, когда они входят,
выходить, когда они выходят; наблюдать, хорошо ли работает протокол, ставка депозитов удовлетворительна,
затем помещать временно неподвижные монеты для получения процентов; Stepn в последнее время популярен,
если импульс все еще растет, то я приму в нем участие.

**3. Какие данные мне нужны, чтобы помочь мне принять решения?**

отслеживать движения активов "умных денег" и даже учитывать объем торгов и распределение активов; проверять TVL протокола, сумму непогашенной задолженности, коэффициент использования капитала, APR и т. д.; учитывать ежедневное количество новых пользователей, тенденции роста, количество активных пользователей в день, количество транзакций, ситуацию с притоком/оттоком игроков и ситуацию с продажами NFT.

- **Три вопроса перед анализом данных**:
  1. Какова моя цель?
  2. Какова моя стратегия?
  3. Какие данные мне нужны, чтобы помочь мне принять решения?

Сложность этих трех вопросов постепенно возрастает. На первые два легче ответить, но трудно четко представлять третий вопрос, что требует много обучения и понимания. Это также небольшой порог, который отличает уровень аналитиков данных. У хорошего аналитика должно быть следующие три качества:

**1. Понимание и распознавание трека или протокола**

То есть, какой трек анализируется? Как работает этот проект?
Какие данные он генерирует и что они означают?

**2. Способность абстрагировать вещи**

Превратить расплывчатое понятие в количественный показатель, т.е.

> "Хорош ли этот протокол DEX?" => "Ликвидность" + "Объем торгов" + "Объем активных пользователей" + "Коэффициент использования капитала" + "Доход, генерируемый протоколом"

Затем вернитесь к предыдущему пункту и найдите соответствующие данные, используя свое понимание протокола.

**3. Способность работать с данными**

Это включает в себя получение данных (откуда берутся данные в блокчейне),
обработку данных (как найти желаемое и отфильтровать нерелевантное)
и возможности визуализации данных.

[Изображение: img/ch00_support.png]

В общем, анализ данных — это просто инструмент для поддержки исследований, поэтому не анализируйте ради самого анализа. Этот процесс начинается с вашего желания исследовать определенный проект, концепцию или трек, затем учитесь и понимайте механизм работы проекта, абстрагируйте количественный анализ качественного понятия и, наконец, собирайте данные и визуализируйте их.

Самое важное в анализе данных — это мышление, ориентированное на данные.
Что касается последнего этапа, то это просто вопрос освоения, который можно разделить на две части:

- Понимание структуры данных блокчейна. Например, в EVM-сетях только аккаунты EOA могут инициировать транзакции, но смарт-контракты могут переводить ETH при вызове.
Эти внутренние вызовы записываются в таблицу `traces`,
поэтому при запросе таблицы `transactions` внутренние транзакции вызовов будут пропущены.
- Освоение языков программирования, таких как Python, SQL. Освоение базового языка базы данных,
будь то самостоятельное получение данных или использование платформы для данных, может быть весьма полезным.
## Как проводить анализ данных в блокчейне

Хотя платформы анализа данных, такие как Dune, проделали большую работу по сортировке данных за нас, нам просто нужно использовать синтаксис, похожий на SQL, чтобы извлекать необходимые фрагменты из таблицы данных для построения. У большинства людей путь обучения, как я считаю, заключается в спешке к "3 дням, чтобы освоить SQL". После освоения его они снова начинают чувствовать себя потерянными и все еще не знают, как найти нить в клубке шерсти. Почему так? Самое важное в анализе данных — это развитие мышления, ориентированного на данные, а владение языками программирования вторично.

Анализ данных предоставляет количественную перспективу для поддержки принятия решений - это процесс, а не конечная цель. Простые шаги заключаются в том, чтобы прояснить три вопроса и сформировать мышление, ориентированное на данные:

**1. Какова моя цель?**

Определить, является ли монета хорошим временем для покупки сейчас?
Решить, добавлять ли ликвидность в AAVE для получения дохода?
Или хотите узнать, не слишком ли поздно входить в Stepn сейчас?

**2. Какова моя стратегия?**

Стратегия покупки монет — следовать за "умными деньгами", покупать то, что покупают они, входить, когда входят они, выходить, когда выходят они; наблюдать, хорошо ли работает протокол, удовлетворительна ли ставка депозитов, затем помещать временно неликвидные монеты для получения процентов; Stepn сейчас в тренде, если импульс все еще вверх, то я приму в нем участие.

**3. Какие данные мне нужны, чтобы помочь мне принимать решения?**

отслеживать движения активов "умных денег" и даже рассматривать объем торгов и распределение активов токенов; проверять TVL протокола, сумму непогашенной задолженности, коэффициент использования капитала, годовую процентную ставку (APR) и т. д.; учитывать ежедневное количество новых пользователей, тенденции роста, ежедневное количество активных пользователей, количество транзакций, приток/отток игроков и ситуацию с продажей NFT.

- **Три вопроса перед проведением анализа данных**:
  1. Какова моя цель?
  2. Какова моя стратегия?
  3. Какие данные мне нужны, чтобы помочь мне принимать решения?

Сложность этих трех вопросов постепенно возрастает. Первые два проще ответить, но четко определить третий вопрос сложно, это требует много обучения и понимания. Это и есть небольшой порог, который отличает уровень аналитиков данных. Хороший аналитик должен обладать следующими тремя характеристиками:

**1. Понимание и распознавание трека или протокола**

То есть, какой трек анализируется? Как работает механизм этого проекта?
Какие данные он генерирует и что они означают?

**2. Способность абстрагировать вещи**

Преобразовать расплывчатое понятие в измеримый показатель, то есть:

>"Хорошо ли этот протокол DEX" => "Ликвидность" + "Объем торгов" + "Объем активных пользователей" + "Коэффициент использования капитала" + "Доход, генерируемый протоколом"

Затем вернитесь к предыдущему пункту и найдите соответствующие данные, опираясь на свое понимание протокола.

**3. Способность работать с данными**

Это включает в себя получение данных (откуда берутся данные блокчейна),
обработку данных (как найти желаемое и отфильтровать нерелевантное) и возможности визуализации данных.

[Изображение: img/ch00_support.png]

В целом, анализ данных — это просто инструмент для поддержки исследований, поэтому не анализируйте ради анализа. Этот процесс начинается с желания исследовать определенный проект, концепцию или трек, затем учитесь и понимайте механизм работы проекта, абстрагируете количественный анализ качественного концепта и, наконец, собираете данные и визуализируете их.

Самое важное в анализе данных — это мышление, ориентированное на данные.
Что касается последнего шага - сделать это самому, то это вопрос владения, который можно разделить на две части:

- Понимание структуры данных блокчейна. Например, в EVM-сетях только EOA-аккаунты могут инициировать транзакции, но смарт-контракты могут переводить ETH при вызове. Эти внутренние вызовы регистрируются в таблице `traces`, поэтому при запросе таблицы `transactions` внутренние вызовы транзакций будут пропущены.
- Владение такими языками, как Python, SQL. Владение базовым языком баз данных может быть очень полезным, будь то самостоятельное получение данных или использование платформы анализа данных.

## Заключение

Существует множество онлайн-ресурсов или учебных пособий по анализу данных Onchain, но они разбросаны и имеют разное качество. Web3 — это открытый университет, но требует немало усилий для поиска подходящих учебников.

Поэтому команда Sixdegree запустит серию учебных пособий "**Освоение аналитики Onchain**". Ориентированные на практику, они будут сочетать структуру данных блокчейна и синтаксис SQL, чтобы предоставить всем набор вводных учебников, помогающих большему количеству людей освоить навыки анализа данных Onchain и максимально использовать характеристики данных блокчейна, и, в свою очередь, в определенной степени устранить информационную асимметрию. Стройте больше в период медвежьего рынка, начало карьеры аналитика данных Onchain начинается здесь!
## О нас

SixdegreeLab – это профессиональная команда по анализу данных на блокчейне. Наша миссия – предоставлять пользователям точные графики, аналитику и инсайты по данным на блокчейне. Мы стремимся популяризировать анализ данных на блокчейне. Благодаря созданию сообщества, написанию учебных пособий и другим инициативам, мы обучаем аналитиков данных на блокчейне, публикуем ценный аналитический контент, продвигаем сообщество для создания слоя данных блокчейна и развиваем таланты для широкого будущего приложений данных блокчейна. Добро пожаловать на площадку обмена опытом!

- Веб-сайт: [sixdegree.xyz](https://sixdegree.xyz)
- Twitter: [twitter.com/SixdegreeLab](https://twitter.com/SixdegreeLab)
- Dune: [dune.com/sixdegree](https://dune.com/sixdegree)
- Github: [https://github.com/SixdegreeLab](https://github.com/SixdegreeLab)

## Ссылки
1. [Эра капитальной эффективности DeFi](https://blog.hashflow.com/the-capital-efficiency-era-of-defi-d8b3427feae4)
2. [Использование ончейн-данных для политических исследований: Часть 1](https://policy.paradigm.xyz/writing/using-Onchain-data-for-policy-research-part-1)
3. [IOSG: Анализ текущей ситуации и перспективы платформы анализа ончейн-данных](https://foresightnews.pro/article/detail/8473)
4. [Введение в «ончейн»-анализ](https://www.blockstar.ch/post/an-introduction-to-Onchain-analysis)
5. [Архитектура приложения Web 3.0](https://www.preethikasireddy.com/post/the-architecture-of-a-web-3-0-application)
6. [Sixdegree Dune Dashborads](https://dune.com/sixdegree)
