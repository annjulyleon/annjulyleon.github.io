---
title: IDEA Community - рисуем диаграммы
tags:
  - plantuml
  - ru
categories:
  - docops
---

Рисуем красивые диаграммы (для документации или чтобы разобраться в коде) в IDEA Community (проверено с версией 2019.1).

Для диаграмм нам понадобится:

  - сама Idea Community/Ultimate (https://www.jetbrains.com/idea/);
  - Graphviz (https://www.graphviz.org/);
  - плагины (плагины ставятся в IDEA \> Settings \> Plugins \>
    Marketplace \> Install \> Restart IDE):
      - [PlantUML Integration](https://plugins.jetbrains.com/plugin/7017-plantuml-integration) - генерит PlantUML диаграммы с помощью
        Graphviz’a. Диаграммы можно сохранять в png, svg;
      - [SketchIt](https://plugins.jetbrains.com/plugin/10387-sketch-it-) - генерит диаграммы классов из кода. Работает для всего
        проекта. Если проект слишком большой (много модулей), может
        упасть с ошибкой. В таких случаях можно временно исключить
        отдельные модули (Project Strcutrue \> Modules \> выбрать
        модуль \> Excluded и сгенерить по частям;
      - [SequenceDiagram](https://plugins.jetbrains.com/plugin/8286-sequencediagram) - генерирует диаграммы последовательности для
        класса/метода.

Для **Eclipse**:

  - [ObjectAid UML Explorer](https://www.objectaid.com/) (плагин).
    Диаграмма классов - бесплатно, диаграмма sequence - нет.

Чтобы сгенерировать диаграммы классов SketchIt\!, нужно выбрать в меню Tools \> SketchIt\! (в версии 2019.1 еще не обновленный плагин, так что пункт меню будет “Text Boxes”). Сгенерированные UML-файлики появятся в корне каждой папки/класса/пакета. Откройте файл, чтобы посмотреть диаграмму в панели справа (там будет вкладка PlantUml). Кстати, так можно в IDEA смотреть любые plantuml диаграммы.

Чтобы сгенерировать диаграмму последовательности, нужно установить курсор на методе, кликнуть ПКМ и выбрать пункт в меню - Sequence Diagram. Появится меню настроек, для начала можно оставить по умолчанию, потом - подкручивать под свои нужды. Диаграмма появится в нижней части экрана. Диаграмму можно сохранить в виде картинки или текста. 
