---
title: Проверка орфографии с LanguageTool и Vale
toc: true
toc_label: "Содержание"
tags:
  - ru
categories:
  - docops
---

В хорошей документации количество орфографических, синтаксических и стилистических ошибок должно стремиться к нулю. К сожалению, при работе с большими объемами добиться этого не так просто. Особенно, если в компании, например, отсутствует культура peer review или технический писатель один одинешенек. Надеюсь, вам повезло, и ваша компания не из таких :). К счастью, существуют инструменты, которые делают жизнь чуточку проще. В этом посте я расскажу, как прикрутила целых две системы проверки к своим проектам.

# Проверка орфографии с LanguageTool и Vale

Сегодня в меню:

- установка и настройка LanguageTool;
- интеграция в VS Code;
- установка и настройка Vale;
- интеграция Vale в VS Code.

## LanguageTool

[LanguageTool](https://languagetool.org/ru) -- это достаточно известный сервис проверки орфографии. У него есть [Premium](https://languagetool.org/ru/premium) облачная версия, [selfhosted версия](https://github.com/languagetool-org/languagetool) и несколько fork'ов.
Я использую fork [libregrammar](https://github.com/TiagoSantos81/libregrammar) by [TiagoSantos81](https://github.com/TiagoSantos81) для standadlone версии. Эта версия поддерживает все нужные функции, включая API, и у нее расширенный словарь и набор правил.
Обычный LanguageTool с правилами от libregrammar удобно использовать в docker-контейнере.

Вот что нужно сделать, чтобы все заработало:

1. Установить languagetool нужной версии. Ниже будет описана установка LanguageTool standalone и в docker-контейнере.
2. (Опционально) Подключить n-граммы.
3. (Опционально) Подключить fastText (_не знаю, экстремистская или это библиотека, или еще (уже) нет?_).
4. Установить для VS Code расширение [LanguageTool Linter](https://marketplace.visualstudio.com/items?itemName=davidlday.languagetool-linter), настроить подключение к запущенному LanguageTool.
5. Запустить проверку командой или при сохранении файла.
6. При необходимости -- добавить в словарь новые слова. Я добавляю в файл, но также можно добавить и через [REST API](https://languagetool.org/http-api/#/default).

### LanguageTool Standalone

Почему может пригодиться Standalone версия: когда нужно просто запустить сервер на Windows и не нужен fastText. Проще тестировать правила и редактировать "внутренности".

1. Установить Java (Java RE 8/OpenJDK).
2. Скачать [релиз](https://github.com/TiagoSantos81/languagetool/releases) и распаковать.
3. В распакованной директории запустить сервер командой `java -cp languagetool-server.jar org.languagetool.server.HTTPServer --port 8081`.
4. Запуск GUI: выполнить команду `java -jar libregrammar.jar`.

### LanguageTool Docker с плюшками

Почему используем docker: чтобы корректно собрать и подключить правильную версию fasttext. Просто запускать где угодно, сложно сломать. Решает проблему с ошибкой `WARN org.languagetool.language.LanguageIdentifier Cannot consider noopLanguages because not in fastText mode: [ru, uk, be]`. В работе использую именно docker-версию.

Базовая версия Docker доступна по ссылке [https://hub.docker.com/r/meyay/languagetool](https://hub.docker.com/r/meyay/languagetool). Однако, эта версия не включает fasttext. Вот в [этом](https://github.com/Erikvl87/docker-languagetool/issues/25) обсуждении нашла версию `Dockerfile`, которая сработала.

1. Создать рабочую директорию.
2. [Загрузить n-граммы](https://languagetool.org/download/ngram-data/untested/) и распаковать в рабочую директорию. N-граммы должны лежать по пути вида `ngrams/ru` (`ngrams/ru/1grams` и т.д.).
3. Создать `Dockerfile`:
  ```text
  FROM alpine as ftbuild
  
  RUN apk update && apk add \
          build-base \
          wget \
          git \
          unzip \
          && rm -rf /var/cache/apk/*
  
  RUN git clone https://github.com/facebookresearch/fastText.git /tmp/fastText && \
    rm -rf /tmp/fastText/.git* && \
    mv /tmp/fastText/* / && \
    cd / && \
    make
  
  RUN wget https://dl.fbaipublicfiles.com/fasttext/supervised-models/lid.176.bin
  
  RUN wget https://languagetool.org/download/ngram-lang-detect/model_ml50_new.zip
  
  FROM erikvl87/languagetool
  
  COPY --chown=languagetool --from=ftbuild /fasttext .
  COPY --chown=languagetool --from=ftbuild /model_ml50_new.zip .
  COPY --chown=languagetool --from=ftbuild /lid.176.bin .
  
  ENV Java_Xms=512m
  ENV Java_Xmx=1500m
  ENV langtool_fasttextBinary=/LanguageTool/fasttext
  ENV langtool_ngramLangIdentData=/LanguageTool/model_ml50_new.zip
  ENV langtool_fasttextModel=/LanguageTool/lid.176.bin
  ```
4. Запустить docker командой:

```text
docker run -d --name="Languagetool" -p 8081:8010/tcp -e langtool_languageModel=/ngrams -v /ngrams:/ngrams --restart=unless-stopped docker-languagetool-fasttext
```

Готово! LanguageTool скачает, установит, запустит все необходимые пакеты и подключит n-граммы.
Вроде бы все хорошо, но ведь нам нужно как-то добавлять новые слова в словарь. Это можно делать и через REST API. А можно просто смонтировать в образ локальную версию файлов со словарем. Вот куда я добавляю новые слова:

- `LanguageTool/org/languagetool/resource/ru/added_custom.txt`
- `LanguageTool/org/languagetool/resource/ru/hunspell/spelling.txt`

Например, в `spelling.txt` добавим все словоформы:

```text
видеофайл
видеофайла
видеофайлам
видеофайлами
видеофайлах
видеофайле
видеофайлов
видеофайлом
видеофайлу
```

А в added_custom.txt добавим словоформы с позиционными тегами.
В [документации](https://github.com/languagetool-org/languagetool/blob/master/languagetool-language-modules/ru/src/main/resources/org/languagetool/resource/ru/tagset.txt) можно найти описание позиционных тегов для русского языка, а, например, вот [здесь](https://pymorphy2.readthedocs.io/en/stable/user/grammemes.html#grammeme-docs) есть описание с более понятными примерами. Я просто ищу похожее слово и копирую теги оттуда.

После добавления новых слов в словарь, сервер нужно перезагрузить. При добавлении через REST API сервер перезагружать не надо.

```text
видеофайла    видеофайл    NN:Inanim:Masc:Sin:R
видеофайлам    видеофайл    NN:Inanim:Masc:PL:D
видеофайлами    видеофайл    NN:Inanim:Masc:PL:T
видеофайлах    видеофайл    NN:Inanim:Masc:PL:P
видеофайл    видеофайл    NN:Inanim:Masc:Sin:Nom
видеофайл    видеофайл    NN:Inanim:Masc:Sin:V
видеофайле    видеофайл    NN:Inanim:Masc:Sin:P
видеофайлов    видеофайл    NN:Inanim:Masc:PL:R
видеофайлом    видеофайл    NN:Inanim:Masc:Sin:T
видеофайлу    видеофайл    NN:Inanim:Masc:Sin:D
видеофайлы    видеофайл    NN:Inanim:Masc:PL:Nom
видеофайлы    видеофайл    NN:Inanim:Masc:PL:V
```

Теперь подключим наши файлы в контейнер. Файлы `added_custom.txt` и `spelling.txt` размещены в рабочей директории в папке `ru`:

```console
docker run -d --name="Languagetool" -p 8081:8010/tcp -e langtool_languageModel=/ngrams -v /ngrams:/ngrams --mount type=bind,source=$PWD/ru/added_custom.txt,target=/LanguageTool/org/languagetool/resource/ru/added_custom.txt --mount type=bind,source=$PWD/ru/hunspell/spelling.txt,target=/LanguageTool/org/languagetool/resource/ru/hunspell/spelling.txt --restart=unless-stopped docker-languagetool-fasttext
```

### Настройка в VS Code

В VS Code в настройках [LanguageTool Linter](https://marketplace.visualstudio.com/items?itemName=davidlday.languagetool-linter):

- **Language Tool Linter: Service Type** -- укажите `external1`;
- **›External: Url** -- указать URL сервера (например, http://localhost:8081);
- **Language Tool: Disabled Rules** -- перечислите правила, которые хотите отключить. Я, например, отключаю `UPPERCASE_SENTENCE_START`;
- **Language Tool: Mother Tongue** -- укажите родной язык (например, `ru-RU`);
- добавьте markdown в список **› Plain Text: Language Ids**;
- отключите **› Smart Format: On Type**, если не хотите, чтобы он автоматически вам менял кавычки и тире.

## Vale - линтер для документации

[Vale](https://vale.sh/) - легковесный [open source](https://github.com/errata-ai/vale) линтер для чего угодно. Например, для документации.
По сравнению с LanguageTool в Vale гораздо проще настраивать правила.
В LanguageTool для добавления новых правил нужно редактировать плохо документированные .xml, а в Vale все делается легко и красиво в yaml.

Use case для Vale -- использовать постоянно в работе, LanguageTool -- запускать для вычитки. В [чатике DocOps](https://t.me/docsascode/21048) знающие люди пишут, что правила срабатывают не во всех случаях для русского языка. Возможно это было для старых версий, потому что пока что все мои правила отрабатывали корректно.

### Установка

Даже писать почти не о чем, это просто исполняемый файл.
_Но я же технический писатель с большим опытом исполнения задач вида "они хотят побольше страниц", так что страдайте._

1. Скачать исполняемый файл [на странице проекта](https://github.com/errata-ai/vale/releases).
2. Добавить путь к exe/sh в Переменные среды.
3. В новом терминале выполнить команду `vale`. Если все установлено правильно, то будет выведена справка по командам vale.
4. Если что-то не получилось, можно установить vale рекомендуемым способ для вашей ОС: [https://vale.sh/docs/vale-cli/installation/](https://vale.sh/docs/vale-cli/installation/).

### Настройка в VS Code

1. Скачать [демонстрационный проект Vale](https://github.com/errata-ai/vale-boilerplate). В этом проекте уже есть пример конфигурационного файла .ini, несколько стилей и текст, на котором можно ставить опыты.
2. Открыть проект в VS Code: так сразу можно будет проверить, работают ли правила.
3. Установить расширение для VS Code [Vale](https://marketplace.visualstudio.com/items?itemName=errata-ai.vale-server).
4. В настройках расширения в **Vale** > **Vale CLI: Path** (`vale.valeCLI.path`) прописать `vale`. Это сработает, если путь к исполняемом файлу vale прописан в переменных среды операционной системы или vale установлен рекомендуемым способом.

Конфигурация для проекта описывается в конфигурационном файле `vale.ini` (см. [документацию](https://vale.sh/docs/topics/config/)). Рекомендую добавить `MinAlertLevel = suggestion` (это же можно сделать в настройках расширения), чтобы другие расширения не перезаписывали уровни репорта ошибок.

### Дебаг в VS Code

Что делать, если что-то не работает:

1. Стили должны быть написаны по правилам, описанным в документации. Если хотя бы в одном правиле есть ошибка (например, использование `(` в регулярных выражениях вместо `(?:`), то весь стиль перестанет работать.
2. Включить терминал (**Terminal** > **New Terminal**). Переключиться на вкладку Output и выпадающем списке правее выбрать Vale. Если с правилами что-то не так, Vale все свои жалобы напишет сюда.
3. Включить Developer Tools (**Help** > **Toggle Developer Tolls**) и посмотреть, кто на что ругается в консоли. Также сюда Vale подробно пишет отчеты о своей работе.
4. Обычные способы борьбы с конфликтующими расширениями: выключить ненужные, деактивировать/активировать все расширения, удалить/установить все расширения.

### Стили

Для работы Vale в папке проекта требуется: файл настроек `vale.ini`, стилевые файлы в `styles/<Название стиля>/` и словарь в `styles/Vocab/<Название словаря>/`.

1. В корне проекта с документацией создать файл `vale.ini`. Пример содержимого:
2. В ключе `BasedOnStyles` указан список стилей (правил), которые Vale будет использовать при проверке. Можно указать одновременно несколько стилей через запятую.
3. В ключе `StylesPath` указан путь к папке со стилями. Это может быть относительный путь (например, `styles`) или абсолютный (например, `D:\Library\vale\styles`).
4. Создать по крайней мере один стиль со стилевым файлом. Например, `styles/InforionGost/Repetition.yml`. Это правило будет отслеживать повторяющиеся слова.
  ```yml
  extends: repetition
  message: "'%s' повторяется!"
  level: warning
  alpha: true
  action:
    name: edit
    params:
      - truncate
      - " "
  tokens:
    - '[^\s]+'
  ```
5. Правило будет сразу же применено при следующем сохранении документа.
6. Документация по составлению правил: [https://vale.sh/docs/topics/scoping/](https://vale.sh/docs/topics/scoping/). В правилах можно использовать регулярные выражения. Так как Vale работает на Go, то для тестирования regex выражений, например, в [https://regex101.com/](https://regex101.com/) нужно выбирать режим Golang.

Если вы пишете на английском, то для вас уже существует множество стилевых файлов от разных компаний, включая Microsoft. В этих же файлах представлены примеры реализации разных проверок, некоторые можно адаптировать для русского языка.
Несколько стилей уже входит в [демонстрационный проект Vale](https://github.com/errata-ai/vale-boilerplate), другие можно скачать дополнительно:

- [Google](https://github.com/errata-ai/Google);
- [IBM](https://github.com/errata-ai/IBM);
- [Openly](https://github.com/testthedocs/Openly);
- [Splunk](https://github.com/splunk/vale-splunk-style-guide) -- есть интересные примеры с использованием позиционных тегов и паттернов.

Для русского языка готовых стилей в свободном доступе я, к сожалению, не нашла.

Самые простые use case, которые сразу же можно быстро настроить и начать применять:

- сленг ("железка", "конфиг", "виртуалка", "задеплоить"). Это же можно сделать с использованием словарей;
- глаголы в первом и втором лице;
- следить за использованием правильных наименований (ViPNet, а не VipNet, PowerShell и не как БГ на душу положит);
- некоторые прилипчивые канцеляризмы (а ля "функционирование" и "с целью" в слишком больших количествах);
- настроить правила орфографии стандартные (например, единицы измерения) и специфические для проекта;
- правила применения ГОСТ (по крайней мере те, которые можно формализовать регулярным выражением).

Несколько примеров:

```text
# Замена неправильного слова на правильное
extends: substitution
message: "Сленг! Используйте '%s' вместо '%s'"
level: warning
ignorecase: true
action:
  name: replace
swap:
  дебаг: отладка
  конфиг: конфигурационый файл
  конфиг(?:а|у|ом|е|ах): конфигурационый файл
```

```text
# Просто подчеркивает нехорошие слова
extends: existence
message: "Это специфичный для проекта сленг ('%s'). Замените!"
level: error
scope: text
ignorecase: true
tokens:
  - сямочк(?:а|и|ой) # не спрашивайте
  - железк(?:а|и|ой)
```

```text
# Местоимения
extends: existence
message: "Не используйте местоимения первого лица '%s'."
ignorecase: true
level: warning
nonword: true
tokens:
  - \b Я \b
  - \b меня \b
  - \b мой \b
  - \b моя \b
  - \b мои \b
  - \b моего \b
  - \b моим \b
  - \b мы \b
  - \b наш \b
```

### Словари

Словари позволяют "подкручивать" единые наборы стилей под конкретный проект. Например, в примере выше для проектов по исследованию устройств настолько часто встречается сленг "сборка" и все к нему привыкли, включая пользователей, что использовать что-то другое просто невозможно. Поэтому конкретно для этих проектов слово "сборка" добавлено в [словарь](https://vale.sh/docs/topics/vocab/) `accept.txt` и правила на них не ругаются.

- все слова в `accept.txt` не будут учитываться правилами (даже если слово в правиле есть);
- все в слова в `reject.txt` добавляются в правило existence (вхождения будут отмечены как ошибки).

Словарь размещаются по пути `StylesPath` из `vale.ini` в папке `Vocab`.
Словарей может быть несколько, для каждого создается своя субдиректория (`Vocab/Reverse`, `Vocab/ML` и т.д.).
В каждой субдиректории должно быть два файла (даже если они пустые): `accept.txt` и `reject.txt`: каждое слово на отдельной строке.

## Finish

Оба сервиса я использую недолго, но они уже помогли отловить обидные ошибки в документации. А Vale очень помогает в обработке текстов от разработчиков: отлавливании жаргонов, стоп-слов и т.д. Все, что раньше приходилось делать глазами, делает Vale. Это ли не счастье?

Осталось ChatGPT прикрутить куда-нибудь, чтобы он и доку за меня писал :).

## Еще интересные расширения для VS Code

- Статья на habr.com [28 расширений VS Code для разработки документации](https://habr.com/ru/post/698702/). Именно там я нашла Vale, спасибо @Svetafo.
- Набор интересных расширений в виде Extension Pack [VS Code for Writers](https://marketplace.visualstudio.com/items?itemName=danspinola.vscode-for-writers).
- Еще один набор [Extension pack for (technical) writing](https://marketplace.visualstudio.com/items?itemName=ocular-d.writing-extension).
