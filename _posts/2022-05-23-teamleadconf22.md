---
title: KnowledgeConf 2022. Впечатления
toc: true
toc_label: "Содержание"
tags:
  - conf
  - ru
categories:
  - conf
---

[Сайт](https://teamleadconf.ru/moscow/2022/schedule)&nbsp;|&nbsp;[Tg](https://t.me/KnowledgeConfTalks)  

Долгие 2 года ожидания и, наконец, оно свершилось! Выставка KnowledgeConf 2022 и даже оффлайн. В этом году организаторы конференции объединили её с [TeamLeadConf 2022](https://teamleadconf.ru/moscow/2022/schedule), насыпали кучку стендов в выставочной зоне, упаковали все в огромный зал №20 в Крокусе и приправили все вкусняшками (пирожки с яблоком - огонь!). На мой взгляд объединение конференций получилось удачным - меньше повторяющихся тем, больше возможностей выбрать доклад по душе.

**Ключевые слова**: onboarding, адаптация сотрудников, knowledge sharing, обмен знаниями, мотивация, обучение, не изобретаем велосипед, компетенции, коммуникации, задавать вопросы.  
**Кому может быть интересно**: тимлидам, HR, project manager, техническому писателю, руководителю технической поддержки, руководителю QA.  

# KnowledgeConf 2022

## День 1

### Позитивное управление контентом: как выстроить партнерские отношения с коллегами

[Описание](https://knowledgeconf.ru/2022/abstracts/8361) | [Презентация](https://disk.yandex.ru/i/R7eHbVa_JbZBng)

Компания **Positive Technologies** хорошо известна в узких техническиписательских кругах. Они участвуют в профессиональных конференциях, организуют митапы для технических писателей и архитекторов контента, и вообще являются одной из компаний, в которой мечтает работать технический писатель.

Как и многие на тернистом пути внедрения управлениями знаний, коллеги из Postitive Technologies столкнулись с уже известными проблемами:

- дублирование ролей в разных командах;
- потенциально переиспользуемый контент дублируется;
- у контента разные цели и целевая аудитория;
- разный стиль и терминология.

Решение: единая контент-стратегия и красивый удобный инструмент. Единая контент-стратегия включает согласование процессов, терминов и стиля ведения документации.  Красивый инструмент: [SCHEMA ST4](https://www.quanos-content-solutions.com/en/software/schema-st4) (под капотом видимо XML, что печально). Как с помощью инструмента в PT решают задачи можно посмотреть в видео с [Positive Authoring Tools Battle](https://vimeo.com/666007627).

В результате внедрения экономят от 20 до 80% времени разработки документации. Переводят постепенно разные отделы на работу с системой. Тут было интересно в принципе послушать как устроен процесс коммуникации между отделами при внедрении такой системы:

- технические писатели - тут всё понятно: быстрый доступ к информации, много форматов, работа в едином пространстве;
- группа сертификации: сделали для них подготовку документов по ГОСТ, шаблоны. Группа была так благодарна за сокращение рутинной работы, что стала продвигать систему в других отделах. Надеюсь, из-за сокращения работы никого не уволили :)
- продуктовый маркетинг - по запросу отдела сделали красивую матрицу [MITRE](https://mitre.ptsecurity.com/ru-RU/techniques) из исходников JSON;
- отдел образовательных программ: им удобный инструмент для разработки контента, а отделу управления знаниями - контент, который можно заимствовать;
- юридический отдел - лицензионные соглашения - переиспользование контента с автоматической сборкой. Звучит как мечта. Юристы… и в CMS с git, невероятно!
- техническая поддержка - участвуют в разработке сценариев для документации, делятся фидбеком;
- центр компетенций - достаточно сложный процесс, так как много новых документов, много пилотных проектов и пользователей.

Выглядит система очень круто, но понятно, что она не подходит маленьким компаниям, а теперь и вообще её и купить затруднительно. Тем не менее я за коллег не переживаю, так как при отлаженных процессах в конце концов можно перейти на другой инструмент, благо ресурсы и разработчики у них есть. На сессии вопросов был интересный: как оценивают документацию (есть ли метрики). Видимо сейчас они её оценивают только по обращениям в ТП, так как справки к ПО в основном оффлайн, часть документации вообще внутренняя. Но внутреннюю они, наверное, как-то оценивают, но как - не рассказали, так что наверное стандартно (посещаемость, просмотры, поиск).

**Оценка**: 4/5, потому что инструмент, конечно, красивый, но недоступный. Процессы красивые, но завязаны на недоступный инструмент.

### Документирование - это про деньги

[Описание](https://knowledgeconf.ru/2022/abstracts/8326)| [Презентация](https://disk.yandex.ru/i/vYn2YRatyy_-Pg)

Семёна послушать всегда интересно, ведь он руководитель одной из успешных компаний [documentat](https://documentat.io/), которая занимается аутсорсингом разработки документации. 
Главная тема доклада - документация ради документации не нужна, документация разрабатывается с целью: заработать деньги, сэкономить деньги, минимизировать риски (юридические, обращения в техническую поддержку и т.д.). 
В общем, звучит логично, и я с таким в практике сталкивалась, в том числе с ситуациями, когда реально традиционная документация не нужна - видео и комментарии в коде дешевле и лучше подходят целевой аудитории.

Иногда документация сама по себе может стать источником дохода. Семен привел в пример портал знаний [Digital Ocean](https://www.digitalocean.com/community/tutorials) (сама неоднократно пользовалась). Авторы документации DO сами говорят, что немаленький процент их пользователей приходит со страниц портала.

ROI документации:

- мы заработали/сэкономили больше, чем потратили?
- затраты окупились?

ROI для документации посчитать не всегда просто, но можно оценить по косвенным признакам:

- сокращение числа обращений в техническую поддержку;
- ускорение онбординга сотрудников;
- субъективно - тоже можно.

Затраты на документацию:

- время на сбор информации;
- написание текста (разработка контента);
- ревью (да… где то он есть… хнык);
- актуализация (поддержка);
- вычитка.

Интересные парадоксы:

- иногда дешевле не писать документацию. Например, архитектурная документация - самая дорогая. Устный онбординг - менее эффективен, но дёшев;
- это необязательно должен быть текст. Давно хотела писать доку в комиксах :) Или снимать крутые блокбастеры.
- инструмент не важен. Полезный документ Word, лучше бесполезного md в git, которым никто не пользуется (боль :'().

**Оценка**: 5/5. Хотя вроде и говорил прописные истины, но все разложил по полочкам.

### Покажи мне свой Git, и я скажу, кто ты

[Описание](https://teamleadconf.ru/moscow/2022/abstracts/8402) | [Презентация](https://disk.yandex.ru/i/uvDpewgwxxC0mg)

В компании **Evrone** живут адекватные люди, которые не смотрят в экран разработчиков, а вместо этого смотрят в Jira и Git. 

Доклад про паттерны поведения по коммитам, пулл-реквестам и комментам в гите. Как индивидуальные, так и групповые. 
Все паттерны описаны в презентации. Например, "Специалист" - знает как работает, метрики - рефакторинг, стабильные коммиты, мало обсуждений (так как никто больше не знает этот участок кода).

Александр подробно описал разнообразные паттерны поведения, а ведь всегда интересно послушать, как навешивают ярлыки на других людей :) 
К сожалению из доклада непонятно, есть ли в принципе какой-то "адекватный паттерн" и не проще ли было бы подобрать адекватный для проекта и отслеживать от него отклонения? 
Потому что похоже, что описаны были буквально все паттерны поведения в гите, и все они получается, что неправильные. 
Я бы занервничала, так как непонятно, как в этом случае вообще что-либо коммитить, чтобы тебя метрики не записали в какие-нибудь "плюшкины".

**Оценка**: 3/5, сам подход интересный, но метрики кажутся избыточными.

### Как сделать, чтобы базой знаний начали пользоваться, пожалуйста

[Описание](https://knowledgeconf.ru/2022/abstracts/8372) | [Презентация](https://disk.yandex.ru/i/hnV5GsJEZziaag)

Тернистый путь к базе знаний в компании **Veeam**. Такие доклады я про себя называю инструментально-процессный: описывается конкретный инструмент, с какими проблемами столкнулись и как пришлось менять или адаптировать процессы в компании.

Базу знаний запилили на [MediaWiki](https://www.mediawiki.org/wiki/MediaWiki) с анализатором метрик [Matomo](https://www.mediawiki.org/wiki/Extension:Matomo) и расширением семантики [Semantic Mediawiki](https://www.semantic-mediawiki.org/wiki/Semantic_MediaWiki) (полезная штука, надо пощупать). 

Как обычно, сначала столкнулись с непониманием и нежеланием, что-либо делать добровольно :). 

- Прикрутили визуальный редактор - дело пошло быстрее. Да… я тоже думала, что может быть для разработчиков лучше, чем markdown + git? А вот поди ж ты…
- Автоматизируют рутину: сбор данных из баз данных, генерация типовых данных. Это еще больше повысило вовлеченность в процесс, так как рутинные операции делать больше не требуется.
- Также интересно, что прикрутили поисковые синонимы - когда одна и та же сущность называется по-разному в разных отделах, поиск можно выполнить по аллиасам в том числе. Крутая функция. А некоторые выше терминологию согласовывают, наивные.
- Кроме того, ребята прикрутили новости и информацию о статьях на главный сайт. Пользователи видят, что базой пользуются, их статьи читают, люди пишут, и сами начинают писать.

**Оценка**: 5/5. Всегда здорово, когда все хорошо заканчивается - база знаний заработала и ей пользуются.

### Языки разработки процесcов

[Описание](https://teamleadconf.ru/moscow/2022/abstracts/8446) | [Презентация](https://disk.yandex.ru/i/s1wYXcq7E1ayLQ)

Интересный доклад, который скорее тянет на лекцию. Инженерный подход к управлению с выкладками из серьезной литературы, терминами, рисунками и даже формулами. 
Как построить управление командами и людьми, откуда брать знания (из психологии, социологии, математики), другими словами - какие дисциплины можно использовать для описания процессов управления.

Принципы социально-технической системы, которой нужно управлять:

- Технологическая система расширяема и модифицируема
- Человек как развиваемый ресурс
- Оптимальная группировка задач
- Самоконтроль
- Сотрудничество и коллегиальность
- Доверие

Психология помогает предсказывать поведение людей, но за людьми нужно наблюдать (собирать информацию, метрики). 
Задача менеджера наблюдать и предсказывать. *Интересная мысль*: чтобы у человека был максимальный результат, его мотивация должна быть средней (не высокой, во как!) – *закон Йеркса-Додсона*. 
Социальная психология изучает, что люди думают друг о друге и как влияют друг на друга – управление командами и из взаимодействием. 
В общем, люди – это нелинейный системы, и задача менеджера понимать, что то, что для одного человека – проблема, для другого – может быть тривиальной задачей (в презентации формулы красивые). 

Короче говоря, для каждого процесса в управлении можно взять какой-то инструмент из смежной дисциплины. 
Например, из моделирования - обратная связь, обработка ошибок. Это и есть язык описания процессов. 
В докладе есть примеры, но не всегда их можно однозначно связать между собой. _Тот случай, когда включается синдром самозванца и сидишь и думаешь, может я просто тупая?_

**Оценка**: 3/5. К сожалению, не уложились в тайминг и осталось чувство незавершенности. Плюс структура доклада и логика повествования несколько сбивают с толку, так как нет логичных переходов между темами. Несмотря на это, доклад стоит потраченного времени, хотя было бы проще его воспринимать в виде статьи.

### Онбординг для социофобушков

[Описание](https://knowledgeconf.ru/2022/abstracts/8281) | [Презентация](https://disk.yandex.ru/i/ya8_PeXjMlhWDA)

Актуальная тема для социофобушка. Доклад про то, как сложно социфобушку проходить онбординг и вообще адаптироваться к новой команде, и можно ли как-то ему помочь.

Как бывает:

- кидают в воду: вот проект, вот где инфа, спрашивай - жиза :)
- вот Юля (Максик), он все расскажет (неть, у него своих дел по горло);
- устный онбординг.

Как надо:

- предлагает чек-лист, в котором нужно помимо обычной инфы, указывать контакты людей (бухгалтеров, эйчаров);
- краткое о компании (это я не поняла зачем, кратко я в интернете почитаю);
- о клиентах;
- про проект - стандартно, стэк, технологии, процессы.

Придумали систему "Бадди" – лучший друг в команде, в которой вы ничего не знаете. Лучше всего, чтобы это был не руководитель, не эйчар, не коллега из той же команды (видимо если команд много). 
Кто же это? Эксперт, лидер направления, коллега, который метит в руководители, с эмпатией. 
База знаний - любимый инструмент онбординга социофобушков. Должны быть описаны процессы, ключевая техническая информация, специфика работы каждой команды.

_В общем, в первой части доклада на мой взгляд описана стандартная "хорошая" система онбординга. Потому что не будут же делать в компании две странички в вики онбординга - одну для "нормальных" людей, вторую - для социофобушков. Если информация изложена четко, процесс отлажен, то и социофобушку, и обычным людям будет проще. Ситуация, когда "кидают в воду" означает, что процесс не отлажен или отсутствует вообще._

Вторая часть доклада про коммуникации. Прозрачность коммуникаций - важная часть процесса. В этой части доклада объединились основы делового этикета и рабочих процессов в компании. Ключевые моменты:

- система заявок (очередной ops - ItOps) – тикеты для рабочих инфраструктурных вопросов (поднять виртуалку, заменить мышку), шаблоны для заявок;
- чат-боты с информацией (типа заявление на отпуск форма, получить справку);
- календарь встреч - встречи планируем заранее, внезапные встречи - зло (ДА!). Для встречи формируем тему, список участников, пункты обсуждения.

Рассылка новым сотрудникам:

- чек-лист;
- что нужно сделать;
- какие ожидаются результаты (как понять, что ты все сделал как надо) - *definition of done*;
- сколько должно занять времени.

Для социофобушков коммуникация – это стресс. Я даже скажу больше – внезапная коммуникация, это еще больший стресс.
Поэтому социофобушки так любят, чтобы тема встречи была известна заранее и чтобы им писали, а не звонили.

**Оценка**: 4/5. С одной стороны - приятно, что такая проблема в принципе поднимается. С другой стороны, когда социофобушка выделяют в отдельную касту людей с каким-то отдельным чек-листом – это еще хуже. Вероятно, лучше сосредоточится на комфортном процессе онбординга для всех и формировании культуры деловой этики в компании, это будет всем полезно.

## День 2

### Учимся задавать вопросы осмысленно

[Описание](https://knowledgeconf.ru/2022/abstracts/8297) | [Презентация](https://disk.yandex.ru/i/_3cYxp33e2tPow)

Очень актуальная для меня тема, так как я в своей практике не раз замечала, что правильно заданный вопрос - половина дела. Кроме того, чтобы подобрать правильный вопрос - нужно разбираться в области задач. Доклад как раз про это.

Чем лучше составлен вопрос, тем короче итерация (уточнение, переспрашиваем, отвечаем..). Какие могут быть проблемы?

- не копать глубоко - остановится на первом "простом" ответе и не задать уточняющий вопрос. Частая проблема начинающих технических писателей;
- бездумные ленивые вопросы ("расскажи как это работает" vs более конкретно "какой алгоритм применили для классификации по категориям"). Отмечу, что и общие "ленивые" вопросы, иногда стоит задавать, возможно человек расскажет что-то, о чем ты сам не подумаешь спросить. По ситуации;
- мне все понятно - на самом деле не понятно;
- устный эмоциональный контакт - надо уметь слушать;
- некомфортно задавать вопрос: мало опыта, боишься показаться глупым, боишься получить некомфортный ответ.

Как научить задавать вопросы?

- разбирать конкретные кейсы (надо записывать аудио);
- показывать инструменты и методики;
- сформировать атмосферу, в которой не страшно задавать вопросы.
- умение задавать вопросы - это привычка, которую нужно практиковать.

Некоторые методики:

- "шквал вопросов" - полезна для генерации идей;
- техника погружения - 5 почему, чек-листы, шаблоны вопросов (если известна технология - мы уже скорее всего спрашивали и знаем, что спросить);
- техники сомнения (*а что если не?*, *представь, что ты врач… поставишь диагноз?*, искать опровержение, а не подтверждение);
- техники уточнения:
  - повторение вопроса + уточнение (*я правильно понял…?*)
  - переформулируем + уточнение;
  - попросить пример.

Учимся держать паузу. 20 с - нормальная пауза в разговоре на подумать.

Тренируемся слушать:

- не уходить в свои мысли;
- не перебивать;
- проявлять эмпатию.

**Люди запоминают лучше то, что говорят сами**. Как использовать вопросы для помощи (например, при ревью):

- вопросы сомнения - задать наводящий вопрос (а сможет это решение осилить 500 транзакций в секунду?)
- вопросы расширение;
- вопросы, запускающие мышление.

**Оценка**: 5/5. В работе технического писателя умение задать правильный вопрос - залог качественной документации. Я не задумывалась над этим, пока не пришлось поработать с людьми, которые задают ленивые вопросы или вообще не знают, как их задавать. Так что да, это проблема. Взяла для себе несколько техник на вооружение.

### У меня сгорают люди — борьба с выгоранием себя и сотрудников

[Описание](https://teamleadconf.ru/moscow/2022/abstracts/8551) | [Презентация](https://disk.yandex.ru/i/poghVn0kYDR6aA)

Актуальность темы выгорания в IT можно было легко оценить по количеству сидящих в самом большом зале конференции. 
Люди сидели плотным слоем и иногда даже в проходах. Это такой доклад, который стоит послушать и посмотреть, докладчик выступает очень здорово, жизненно, за душу берет.

Общие черты:

- "так у всех" - раз все себя так (плохо) чувствуют, значит проблемы нет (есть);
- как можно выгореть, когда делаем то, что нравится?! С другой стороны, мы можем долго что-то делать, не выгорая (играть в героев ^_^).

Повышение зарплаты влияет около полугода, дальше - воспринимается как должное. Также работает некоторое время идея. Что работает? Эмоция.

Нам нравится работать, когда работается хорошо - мы находимся в "потоке". Решаем задачу, она интересная, мы может её решить (навыки соответствуют), она не слишком простая. 
Находясь "в потоке", в концентрации мозг получает удовольствие от того, что ему удалось решить задачу. То есть, нам нужно максимизировать нахождение в этом состоянии. 
Автор предложил схему "капсул", при которой рабочее время разбивается на три отрезка: работа, инфраструктурные задачи и отдых.

![Схема капсул](/assets/images/posts/2022-05-23_capsules.png)

Если разбивать на отрезки по 2 ч, то: 

- 1 ч – работа в потоке: работаем над задачей, не отвлекаясь (выключить уведомления, звук, не отвлекаемся);
- 30 мин – инфраструктура (установка вм, митинги, обсуждения);
- 30 мин – отдых. Отдых не за компьютером - пойти погулять, пообедать, полежать.

Говорит, система работает так, что если соблюдать отрезки (понятно, что конкретную продолжительность можно менять), то мозгу захочется вернуться к работе, к решению задачи. Надо попробовать :)

**Оценка**: 5/5. С одной стороны, доклад здоровский, нужный, важный, актуальный. И вроде даже волшебная капсула есть. Но я не понимаю, как это применять, ведь часто находясь в потоке - сложно из него вырваться, так как следующий вход может занять опять много времени. 

### Как выжить в тотальной неопределенности

[Описание](https://teamleadconf.ru/moscow/2022/abstracts/8472) | [Презентация](https://disk.yandex.ru/i/1bYgJrWIN5rIUw)

Я бы назвала этот доклад рассказом в обществе анонимных менеджеров проектов. Привет, я менеджер проектов, я ничего не понимаю, что вообще происходит, куда бежать! *Хлоп, хлоп, хлоп*. К сожалению, история с грустным концом, так как после 24.02 проект пришлось свернуть… *Хлоп, хлоп, хлоп*.

Дано: нужно вывести внутренний продукт на рынок. Есть: одинокий, но боевой менеджер проектов Настя :).

Что делать?

- найти экспертов, у которых можно спросить - в сообществах, в linkedin. Установить контакт;
- интервью с экспертами и менторами (теми, кто может решить задачу или решал подобные);
- интервью с аудиторией – не делать поспешных выводов;
- не забывать про себя – хвалить себя за достижения;
- не выгорать и управлять своим временем.

Оценка: n/a. Вряд тут можно поставить оценку, так как это просто история одного проекта. Интересно рассказана. Презентация так вообще лучшая на конференции, крыть нечем.

### 15 приемов, чтобы задать вопрос профессионалу

[Описание](https://knowledgeconf.ru/2022/abstracts/8358) | [Презентация](https://disk.yandex.ru/i/jptbOfxeBhSBLQ)

В продолжение темы про вопросы – еще немного приемов, как говорить с экспертом. Прежде всего – разочарование. 
На самом деле в докладе говорится про 7 приемов, а про остальные предлагается почитать по ссылке. В корне неправильный подход, я считаю. Лучше назвать доклад "7 приемов…" и в качестве сюрприза предложить еще целых 8 (!) по ссылке. Кругом обман!

Зачем разговаривать с экспертами? В их головах самые актуальные и нужные знания, которых еще нигде нету. Как определить, кто эксперт? Сравниваем результаты в области по стране, региону, миру. Всегда нужно понимать, что цель общения с экспертом - извлечь знания.

1. Сформулировать тему и цель интервью. Изучить тему, предыдущие интервью с экспертами, тема должна быть конкретной. Результат - формулировка того, что нам нужно узнать.
2. Проверить уровень своей готовности: быть хорошо осведомленным в теме. Недопустимо, чтобы эксперт тратил время на известные в области термины и темы.
3. Узнать больше об эксперте - его карьере, местах работы, предыдущих интервью, статьях, докладах.
4. Составить вопрос. Иногда эксперт уходит от вопроса, что делать в этом случае?
   1. подготовить примеры (лучшие практики, ошибки);
   2. попросить привести пример.

Итак, знаменитые 7 приемов (или о чем спрашивать эксперта):

1. Задать ориентир (эталон). Какой результат оптимален? На кого равняться? Анти-результат.
2. Формула успеха. Назовите шаги алгоритма на пути к цели. Вредные советы (что НЕ делать).
3. Воспроизводимый опыт: случай из практики, как действовать в ситуации X.
4. Провалы и трудности: типовые ошибки, рекомендации как избежать.
5. Единичный прием: пример задачи – как вы это делаете? что вам помогает в задаче X.
6. Сравнение – подготовить пример. Что из этого хуже (лучше), какие отличия x и y?
7. Переменные проблемы: какие белые пятна? какие проблемы, которые не удалось решить?

Подготовка чек-листа:

- достаточно ли ясно сформулировано;
- достаточно ли информации;
- цель каждого вопроса;
- логика и порядок вопросов.

Как закончить интервью:

- договориться о встрече (если нужно);
- предложить прислать сводку с выводами (эксперт может предложить что-то еще после прочтения);
- рекомендации других экспертов.

**Оценка**: 4/5. Интересный доклад, но 8 приемов зажали. 

### Как создать систему управления знаниями на 1к человек: от продуктовых команд к кастомер-фейсинг и обратно

[Описание](https://knowledgeconf.ru/2022/abstracts/8303) | [Презентация](https://disk.yandex.ru/i/06YHInMaL2IDVg)

Тот случай, когда вроде начало скучное-обычное "почему KM важен-нужен", а потом как понеслась! Доклад получает приз моих личных симпатий на этой конференции. Рекомендую посмотреть видео, много конкретных и интересных мыслей по процессам управления знаниями.

Управление знаниями - это процесс. Процессы - это в том числе про деньги. Процессы создают, ими управляют, их контролируют. В компании **Exness** процессы KM встроены во все процессы компании, это неотъемлемая её часть - как для сотрудников, так и для клиентов. Это здорово. _Хорошо им там на Кипре…_

Кто должен участвовать в процессе? Все! Управлять процессом должны специальные люди (а не какой-то чувак на добровольных началах, типа как тут в одной компании выше). Сколько людей нужно для построения процесса? Нужны правильные люди, если найден правильный человек - до достаточно 1-2.

Пользователи базы знаний - кто угодно. Носители базы знаний - все. Процесс накопления знаний включает извлечение знаний из голов носителей, обработку и передачу пользователям, а также - управление обратной связью (например, если нужно что-то уточнить).

**Важная мысль**: задача управления знаниями – настроить потоки передачи знаний, а не писать статьи (могу добавить также, что здесь не так важен и инструмент, главное процесс – инструментом может быть что угодно).

В видео автор приводит множество примеров (и даже с блок-схемами), как они умудрились настроить эти процессы. Очень впечатляет. 
В процесс вовлечены все сотрудники компании и отделы, это позволяет им не терять квалификацию, постоянно обучать сотрудников, проводить семинары, тесты, разрабатывать курсы – короче говоря, держать руку на пульсе и повышать квалификацию сотрудников. И клиенты довольны.

Интересная рефлексия на сессии вопросов и ответов. Почему это получилось "там" и не получается "здесь"? 
В западной культуре атмосфера поддерживающая, а здесь - конкурирующая. В поддерживающей атмосфере в коллективе легче делиться знаниями и задавать вопросы. 

**Оценка**: 6/5. Если бы можно было выбрать один доклад из всей конференции, то вот это он.

### Dendron: Zettelkasten на стероидах

[Описание](https://knowledgeconf.ru/2022/abstracts/8414) | [Презентация](https://disk.yandex.ru/i/hux8SoS9-jYoGA)

Конференция была бы не полной без очередной серий приключений Константина на пути к персональной системе управления заметками. Так как тема болезненная, каждый имеет, что по ней сказать, так что сессия вопросов и ответов была … напряженной.

Итак, встречайте, [Dendron](https://www.dendron.so/). Knowledge Management. Redefined. Система управления заметками (markdown) по Zettelkasten но с иерархией, схемой и категориями. 
Надстройка на VSCode. Плюсы: можно сгенерировать статический сайт из заметок (типа сайт с документацией, кстати у них wiki как раз на нем). 
Я пыталась понять, что там есть такого революционного, чего нет, например, в Obsidian, но так и не смогла понять. Возможно, что Obsidian все-таки проприетарный?

Оценка: n/a.
