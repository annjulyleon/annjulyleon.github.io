---
layout: post
title: Selenium. Стандартные решения
---

Selenium - расширение для браузера Mozilla Firefox, позволяющее записывать действия пользователя в браузере (переходы по ссылкам, нажатие кнопок), а затем воспроизводить их. Удобно использовать для тестирования и для автоматизации рутинных действий.

Мне Selenium понадобился для автоматизации огромного количества рутинных действий: создание некоторого числа объектов (создание карточки с информацией, приложение к карточке дополнительных данных, картинок, расстановка точек на карте).

Начинала работу с Selenium с минимальными навыками программирования, поэтому реализация некоторых задач требовала много  гугла :).

### Инструменты:

Selenium Firefox ([download](https://addons.mozilla.org/ru/firefox/addon/selenium-ide/))

Selenium ([download](http://docs.seleniumhq.org/download/), [doc на русском](http://selenium2.ru/docs/selenium-ide.html),[ first steps (видео)](http://www.youtube.com/watch?v=uZcumrvZBOU))

Плагины и дополнения:
- Datadriven (набор js плагинов): datadriven.js, goto_sel_ide.js, user-extension.js ([ссылка](https://sites.google.com/site/seleniumworks/selenium-ide-data-driven), [описание](http://seleniumworks.blogspot.ru/2014/01/selenium-ide-data-driven.html)),
- Sideflow (js плагин): ([ссылка](https://github.com/73rhodes/sideflow))

Пути к каждому .js плагину необходимо указать в строке `Selenium Core Extensions (user-extensions.js)` (Options > Options > General) через запятую, а также отметить экспериментальные опции (Activate Developer Tools, Enable experimental features).
- [File Logging (Selenium IDE)](https://addons.mozilla.org/en-US/firefox/addon/file-logging-selenium-ide/?src=search) - добавляет возможность логирования в файл
- [SelBlocks](https://addons.mozilla.org/en-Us/firefox/addon/selenium-ide-sel-blocks/) - добавляет циклы, условия, функции и параметризацию. [Вики](http://refactoror.wikia.com/wiki/Selblocks_Reference).
- [Selenium IDE Flow Control](https://addons.mozilla.org/ru/firefox/addon/flow-control/) - добавляет возможность [пропускать части теста](http://51elliot.blogspot.ru/2008/02/selenium-ide-goto.html) (goto)
- [Stored Variables (Selenium IDE)](https://addons.mozilla.org/en-us/firefox/addon/stored-variables-viewer-seleni/) - позволяет просматривать значения переменных

Также:
- [Firebug](https://addons.mozilla.org/ru/firefox/addon/firebug/)
- [Selenium Expert](https://addons.mozilla.org/ru/firefox/addon/selenium-expert-selenium-ide/)

### Пример: считать данные из .xml файла

.xml файл вида:

```xml
<testdata>
<test name="Москва" address="г. Москва" long="36.904449485028536" lat="55.980935909051695" region="Центральный федеральный округ" />
<test name="Чертовицкое" address="г. Воронеж" long="39.224118" lat="51.814354" region="Центральный федеральный округ" />
... 
</testdata>
```

```
//Тест необходимо выполнять до конца файла.
loadTestData | file://C:/Selenium/xml/testdata.xml |
while | !testdata.EOF() |
nextTestData | |
//Код, который необходимо выполнить для каждого набора данных. Можно использовать циклы, goto и т.д.
type | id=Town | $(name)
type | id=Long | $(long)
//и.т.д
endWhile | |
```
### Пример: выбор случайного элемента из выпадающего списка

Дано: выпадающий список c id id=type с 4 элементами (в css [option] = 1...4). Необходимо выбрать случайный элемент.
```
//Сгенерировать случайное число по формуле *Math.floor(Math.random() \* (max - min + 1) + min))* и сохранить в переменную randomType
storeEval | javascript{Math.floor(Math.random() * (4 - 1 + 1) + 1)} | randomType
//Сохранить текст с атрибутом option=randomType (сгенерированное число) в переменную setType. 
storeText | //html/body/div[1]/div[2]/section/div/article/form/fieldset/div[18]/select/option[${randomType}] | setType
//Выбрать элемент с текстом setType из выпадающего списка type
select | id=type |label=${setType}
```
### Пример: расстановка точек на плане/карте OpenLayer (с условиями и циклами)

Пример работы с OpenLayer, а также с условиями и циклами.
Selenium IDE работает с OpenLayer довольно коряво или не работает вообще. Моя задача была взять элемент из выпадающего списка id=elements (количество элементов любое или их вообще может не быть - тогда все дальнейшие действия пропускаются, названия элементов любые), выбрать инструмент установки точки в панели инструментов и указать точку на плане (OpenLayer). Для элементов определенного типа еще нужно поставить две точки, обозначающие угол обзора видеокамеры.

Возможно, есть решение попроще, но я решила задачу так:
```
//Перед выполнением действий необходимо установить нужный размер окна браузера. Это поможет правильно рассчитать координаты для расстановки:
runScript | javascript{window.opener.resizeTo(1529,1033);}| 
//Посчитать, сколько элементов в выпадающем списке id=elements (xpath)
storeXpathCount | //html/body/div/div[2]/section/div/div/article/div[1]/span[1]/select/* | numberOfElements
//Если в списке элементов нет, то пропустить весь следующий код и перейти сразу к label | SKIP_PLAN)
gotoIf | ${numberOfTsOnPlan} == 0 | SKIP_PLAN
//Для каждого элемента из списка повторять код между for и endFor (начало и конец цикла)
for | i = 1; i = numberOfElements; i ++ | |
//Считать id ТС, которое хранится в атрибуте option выпадающего списка, и записать в переменную tsId. Значение совпадает с номером итерации цикла i ^
storeValue | //html/body/div/div[2]/section/div/div/article/div[1]/span[1]/select/option[${i}] | tsId
//Выбрать из выпадающего списка selectSensor ТС с tsId
select | id=selectSensor | value=${tsId}
//Нажать инструмент "Установить ТС" на панельке
click | //span[@id='tools']/label/span | 
click | id=putFeature | 
//Сгенерировать две координаты (максимальное и минимальное значение подбирается опытным путем):
storeEval | javascript{Math.floor(Math.random() * (600 - 200 + 1) + 200)} | coord1
storeEval | javascript{Math.floor(Math.random() * (500 - 200 + 1) + 200)} | coord2
//Поставить выбранное ТС в точку с генерированными координатами. Нажать левую кнопку мышки в точке, затем отпустить левую кнопку мышки в точке
mouseDownAt | id=OpenLayers_Map_12_OpenLayers_ViewPort | ${coord1},${coord2}
mouseUpAt | id=OpenLayers_Map_12_OpenLayers_ViewPort | ${coord1},${coord2}
label | SKIP_PLAN |
```

### Пример: выбор всплывающего окна

Всплывающее окна бывают разными, в моем случае - это новое "окно" браузера. Реализовано коряво, но что дают - то и приходится тестировать. Для выбора используется selectPopUp(windowID). windowsID было всегда разное, поэтому пришлось использовать без параметров - тогда выбирается просто самое верхнее окно.

```
//Раздача слонов
//На всякий случай установить размер окна браузера
runScript | javascript{window.opener.resizeTo(1530,1030);}|
loadTestData | file://D:/selenium/ide-testcases/js/DCUs.xml |
while | !testdata.EOF() |
nextTestData | | 

//Открыть нужную страничку, ищем нужного слона
open | /Access | 
type | id=resource-search | ${shortname} |
click | id=resource-search-button | 
click|css=input[type="checkbox"] | 
click|id=add-permissions | 

//Всплывающее окно (15 сек ждать пока появится, так как интерфейс тормозит)
waitForPopUp | | 15000 
selectPopUp | | 
click | css=ins.jstree-checkbox | 
<.....>
click | id=submit-permissions | 
deselectPopUp

//Перезагрузить страницу на случай если заглючит интерфейс
refreshAndWait
<....>
endWhile
```


### Пример: загрузка файла (вставка пути к файлу)

Для множества карточек нужно загрузить план помещения. Путь к файлу плана берется из .xml.

Вид .xml файла:
```xml
<testdata>
 fullname="Тестовый 02" 
 shortname="Тестовый 02" 
 address="г. Москва" 
 <...> 
 long="36.904449485028536" lat="55.980935909051695" 
 plan1="D:\\selenium\\ide-testcases\\plan.jpg" tspath="js/DCU01TS.xml"
 <...>
event1type="ui-multiselect-strategies-option-4" event1name="Попытка покраски полицейского в розовый цвет" 
event2type="ui-multiselect-strategies-option-2" event2name="Сломались все камеры"
event3type="ui-multiselect-strategies-option-1" event3name="Пришел песец и всем показал" 
resultstate="lvl-8"/>
```
```
//Для всех данных из xml файла
loadTestData | file://D:/selenium/ide-testcases/js/DCUs.xml  |
while | !testdata.EOF() |
nextTestData | |

//Найти нужный объект, перейти на нужную вкладку
<...>

//Загрузить план
click | id=newPlanLink | 
pause | 2000 |
type | id=Image | ${plan1}
type | id=Description | План 1
click | xpath=(//button[@type='button'])[2] |
refresh
waitForElementPresent | id=editor-0| 
<...>
endWhile
```

### Пример: Получить ID страницы (объекта) из адресной строки и записать в переменную

Часто приходится работать с созданным объектом, http адрес которого http://<база>/<идентификатор>. Чтобы получить этот идентификатор из строки используется *storeLocation* и javascript.split . В моем случае разделителем был - "1".

```
//На странице с URL с идентификатором сохранить текущее расположение в переменную dcuIdFromUrl
storeLocation | dcuIdFromUrl |

//Вывести в консольку (или лог) для проверки
echo | ${dcuIdFromUrl} |

//Сохранить разделитель в переменную
store | 1 | dcuDelimiter

//Отделить идентификатор от базы и сохраняем в переменнуюstore |  javascript{storedVars['dcuIdFromUrl'].split('View/')[storedVars['dcuDelimiter']]} | dcuId

//Посмотреть, что получилось 
echo | ${dcuId} |
```

### Пример: Проверить работу пунктов выпадающего меню

Есть выпадающий список, в котором перечислены известные пункты меню. Необходимо выбрать каждый пункт меню и проверить, что он работает корректно. Используется: массив для хранения пунктов меню, цикл.
```
//Проверка фильтрации
//Сохранить пункты меню в массив categories
storeEval | new Array('1 категория','2 категория','3 категория','4 категория'); | categories

//Задать счетчик
getEval | myItems=0; | 

//Выполнить до конца массива (для каждой категории)
while | myItems< storedVars['categories'].length |
storeEval |  myItems | myVar

// Вывести в консоль (или в лог ) текущую категорию
echo | javascript{storedVars['categories'][storedVars['myVar']]} |

//Выбрать пункт в меню и нажать кнопку "Найти"
select | id=Category | javascript{storedVars['categories'][storedVars['myVar']]}clickAndWait | //input[@value='Найти'] |

//Проверить, что найденный объект соответствует выбранному меню
verifyText | //td[6] | javascript{storedVars['categories'][storedVars['myVar']]}

// Перейти к следующему пункту
getEval | myItems++; |
endWhile
```
