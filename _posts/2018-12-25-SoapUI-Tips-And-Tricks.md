---
layout: post
title: SoapUI: Tips&Hints по автоматизации тестирования для начинающих
---

# Soap UI: Автоматизация тестирования веб-сервисов

**Soap UI** - удобная программа для автоматизации тестирования веб-сервисов. Обычные функции, вроде отправки и получения SOAP сообщений, работают из коробки. Для дополнительных плюшек, например, подключения БД, понадобиться также скачать соответствующий драйвер. Также Soap UI поддерживает язык программирования Groovy, с помощью которого можно написать достаточно сложные тесты.

**Содержание**:

- <a href="#install">Установка, настройка, полезные ресурсы</a>  
- <a href="#java-usage">Использование java-классов и простой параметризации в тестах</a>  
  - <a href="#gen-randomguid">Генерация случайного идентификатора</a>  
  - <a href="#gen-date">Генерация дат</a>  
- <a href="#params">Параметризация данных</a>  
- <a href="#test-cases">Тест-кейсы: Cookbook</a>  
  - <a href="#db-connect">Подключение к БД и вставка тестовых данных</a>  
  - <a href="#parse-xml">Разбор XML-файла ответа, извлечение и разбор CDATA</a>  
  - <a href="#file">Сериализация файла</a>  
  - <a href="#gen-random">Генерация случайного числа (между) или случайной даты (между)</a>  
  - <a href="#set-get-properties">Установка и получение свойств из проекта или тест кейса</a>  
  - <a href="#paths">Создание, удаление файлов и пути до файлов</a>  
  - <a href="#step-activation">Активировать или деактивировать шаги теста по условию</a>

<a id="install"/>

## Установка, настройка, полезные ресурсы

Прежде всего нам понадобится:

- SoapUI 64 bit Free (гайд для версии 5.3.0). По умолчанию на сайте загружается версия 32bit. Она подходит для большинства задач, но для особенно развесистых тестов разумнее будет использовать 64-битную версию ([ссылка](https://www.soapui.org/downloads/latest-release.html))
- [драйвер JDBC](https://jdbc.postgresql.org/download.html) для подключения к PostgreSQL . Версия 4.2 (файл postgresql-42.1.1.jar);
- [драйвер JBDC](http://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) для подключения к Oracle - ojdbc8.jar (если что, скачивать тут, требуется регистрация);
- драйвер для вашей БД (в статье только PostgreSQL и Oracle);
- поставить [Java 1.8 версии 64](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html);
- для продвинутых операций, типа загрузки файлов по FTP, также понадобится библиотека [Apache commons Net]((http://commons.apache.org/proper/commons-net/download_net.cgi) - файл commons-net-3.6.jar (в архиве commons-net-3.6-bin.zip). Файл необходимо положить в папку (Windows) *%INSTALL_DIR%\SoapUI-5.x.x\bin\ext* 

После этого настроим работу с БД PostgreSQL:

1. запустить один раз SoapUI;
2. закрыть SoapUI;
3. перейти в папку *C:\Program Files\SmartBear\SoapUI-5.3.0* и переименовать папку jre в jre.ignore;
4. положить скачанный файл драйвера postgresql-42.1.1.jar в папки: *C:\Program Files\SmartBear\SoapUI-5.3.0\bin\ext* и в *C:\Program Files\SmartBear\SoapUI-5.3.0\lib*;
5. открыть SoapUI. В логе (в нижней панели есть SoapUI Log) должны быть записи вида *"Used java version: 1.8.0_121" и "Adding [C:\Program Files\SmartBear\SoapUI-5.3.0\bin\ext\postgresql-42.1.1.jar] to extensions classpath"*

Что мы сделали и зачем: по умолчанию SoapUI работает а Java 7, драйвер postgresql-42.1.1.jar дружит с java 8 - в результате скрипты не выполнятся из-за разных версий java. Поэтому мы убрали встроенную в SoapUI java (переименовав папку), заставив его работать с java, которая установлена на компьютере (java 8). В принципе, можно также просто скачать драйвер для Java 1.7, но в дальнейшем может понадобиться использовать вставки кода Java 1.8 для особенно заковыристых тестов.

Теперь для oracle:

1. положить файл ojdbc8.jar в папки C:\Program Files\SmartBear\SoapUI-5.3.0\bin\ext и в C:\Program Files\SmartBear\SoapUI-5.3.0\lib;
2. в Oracle должен существовать юзер, с которым можно подключиться к БД (sys подключиться не даст). Создаем пользователя:

```sql
create user testadmin identified by oracle;grant dba to testadmin;
```

Строка подключения к oracle (примеры использования будут ниже): `jdbc:oracle:thin:testadmin/oracle@10.10.10.10:1521:orcl` , драйвер oracle.jdbc.driver.OracleDriver

Полезные примеры всяких скриптов и решений:

- [Learning SoapUI](https://learnsoapui.wordpress.com/)
- [Сборник примеров скриптов](http://mkarthikeya.blogspot.ru/2011/11/soapui-groovy-snippets.html)

<a id="java-usage" />

## Использование java-классов и простой параметризации в тестах

В SoapUI прямо в SOAP запросах можно использовать простейшие java-функции, например генерировать случайный UUID для сообщения или дату. Вставка кода или переменной вместо значения в запрос выполняется в следующем формате. Начало кода предваряет символ доллара ($), java код или переменная записываются в фигурные скобки `{}`

`${какая-то java фича}`

<a id="gen-randomguid" />

### Генерация случайного идентификатора

Например, функция генерации случайного идентификатора (UUID):

`=java.util.UUID.randomUUID()`

Можно вставить ее прямо в нужное поле, например для элемента XML-сообщения mesId:

`<mesId>${=java.util.UUID.randomUUID()}</mesId>`

Теперь при каждой отправке сообщения в этом поле будет генерироваться рандомный идентификатор, и ничего не нужно менять руками.

<a id="gen-date" />

### Генерация дат

Часто в сообщения XML проставляется дата. Для некоторых тестов иногда также требуется указывать диапазоны дат, или конкретные даты, например - дата и время минус 3 ч от текущего времени. В этом случае также можно воспользоваться функцией java.

Например, вот такая формула сгенерирует текущую дату с указанием таймзоны:

`${=new java.text.SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss.ms+03:00").format(new Date())}`

Получится: `2018-07-31T09:54:02.542+03:00`

Если требуется сгенерировать какую-то дату в прошлом или будущем, можно использовать такой код:

Час назад:

`{=import java.text.SimpleDateFormat; Calendar cal = Calendar.getInstance(); cal.add(Calendar.**HOUR**, **-1**); new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss.ms+03:00").format(cal.getTime());}`

14 дней назад:

`${=import java.text.SimpleDateFormat; Calendar cal = Calendar.getInstance(); cal.add(Calendar.**DATE**, **-14**); new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss.ms+03:00").format(cal.getTime());}`

<a id="params" />

## Параметризация данных

Предположим у нас есть проект, который нужно перенести на другую площадку. Или надо поменять логин или IP во всех запросах. Для всего в этого в SoapUI  можно использовать *свойства проекта (properties)*.

В свойствах проекта можно указывать в виде переменных какие-то значения, которые используются в запросах постоянно, а в самих запросах просто делать ссылку на эту переменную. Например, можно создать переменную, в которой указан логин пользователя, который используется для отправки запроса. В самих запросах указывать переменную. А если нужно перенести запрос на другую площадку, изменить пользователя - просто изменить значение переменной, и везде во всех запросах логин изменится автоматически.

Свойства проекте можно посмотреть в нижней левой панели при выделенном проекте на вкладке *Custom Properties*. Там же можно создать переменную (нажав кнопку с плюсом), задать имя переменной (например, login) и значение (например, u.user).

Чтобы использовать переменную в запросе, нужно сослаться на нее в формате: `${#Project#ИМЯПЕРЕМЕННОЙ}`

например: `${#Project#login)`.

В самом запросе переменную можно вставить например так:
`<userId>${#Project#login}</userId>`

Все переменные из проекта можно сохранить (экспортировать) в файл, а потом импортировать.

<a id="test-cases" />

## Тест-кейсы: Cookbook

Как создавать и из чего сделаны тест-суиты (Test Suite) и тест кейсы (Test Case) довольно здорово описано в официальной документации:

- [структура тестов;](https://www.soapui.org/functional-testing/structuring-and-running-tests.html)
- [тест-степы (Test Step))](https://www.soapui.org/functional-testing/working-with-teststeps.html).

Каждый шаг в тесте может быть определенного типа, например JDBC или SOAP запрос, Groovy скрипт и другие.

<a id="db-connect" />

### Подключение к БД и вставка тестовых данных

!Предложенные ниже скрипты и решения вероятно не являются самыми оптимальными, возможно все можно упростить и сделать красивше :) Практика, гуглирование и чтение документации в этом помогут.

Для подключения к БД будем использовать шаг тест-кейса Groovy Script. Для этого шага в принципе можно было бы использовать JDBC Request (подключение к БД), но для развесистых SQL запросов это не всегда удобно.

В окне редактирования скрипта используем код вида (пример для БД PostgreSQL):

```groovy
 /*Регистрируем jdbc драйвер, Файл драйвера нужно скачать https://jdbc.postgresql.org/download.html здесь, версия 4.2  
 и положить в папки C:\Program Files\SmartBear\SoapUI-5.3.0\lib и C:\Program Files\SmartBear\SoapUI-5.3.0\bin\ext*/  
 com.eviware.soapui.support.GroovyUtils.registerJdbcDriver('org.postgresql.Driver')  
 import groovy.sql.Sql  
 import java.sql.Driver  
 /*Подключение к БД*/  
 def sql = Sql.newInstance("jdbc:postgresql://10.10.10.10:5432/SOMEDATABASE","postgres","postgres","org.postgresql.Driver")  
 /*Выполняем скрипты между ''' (тройными одинарными кавычками)*/  
 sql.execute '''  
 INSERT INTO "SCHEME"."TABLE" VALUES ('231', '2017-04-03 00:00:00', '2018-04-03 00:00:00', '0', '1', 'рандомная строчка', '2');  
 INSERT INTO "SCHEME"."TABLE" VALUES ('232', '2017-04-03 00:00:00', '2018-04-03 00:00:00', '0', '2', 'еще одна рандомная строчка', '2');  
 '''  
```

Этот скрипт, конечно, работает, но можно его немного улучшить. Так как мы можем перемещать наши тесты между разными площадками и IP, было бы здорово, если можно было поменять подключение в свойствах проекта (параметризация и свойства проекта описаны в разделах выше). Для этого в свойствах проекта создаем три новых переменные, например:
pgCon: jdbc:postgresql://192.168.70.91:5432/ORA2PG_T
pgUser: postgres
pgPass: postgres

Тогда строки подключения к БД будут выглядеть так:

```groovy
 /*Здесь мы объявляем переменные, значения которых достаем из свойств проекта (у меня переменные и в скрипте,  
 и в свойствах проекта названы одинаково*/  
 def pgCon = testRunner.testCase.testSuite.project.getPropertyValue( "pgCon" )  
 def pgUser = testRunner.testCase.testSuite.project.getPropertyValue( "pgUser" )  
 def pgPass = testRunner.testCase.testSuite.project.getPropertyValue( "pgPass" )  
 /*Здесь мы просто указываем объявленные ранее переменные*/  
 def sql = Sql.newInstance(pgCon,pgUser,pgPass,"org.postgresql.Driver")  
```
<a id="parse-xml" />

#### Разбор XML-файла ответа, извлечение и разбор CDATA

Ответ от веб-сервиса может прийти в виде простой XML-структуры, которую довольно просто разобрать, не применяя Groovy-скрипты. Также ответ может приходить в виде развесистого XML, а нужные данные еще и в CDATA обернуты.

Предположим есть XML:

```xml
 <soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">  
   <soap:Body>  
    <ns2:processRequestResponse xmlns:ns2="http://www.somesite.ru/services/interfaces/" xmlns:ns3="http://somesite.ru/somesite/data">  
      <return>  
       <header>  
         <createTime>2017-06-01T11:54:31.543+03:00</createTime>  
         <..>some other headers</>  
         <mesId>d4269757-0f32-4034-9eb4-4385eeff14d4</mesId>          
       </header>  
       <transferData><![CDATA[<?xml version="1.0" encoding="UTF-8" standalone="yes"?>  
 <ns4:Message xsi:type="ns5:DataMessage" xmlns:ns6="http://www.somesite.ru/data/onsi/operators/" xmlns:ns5="http://www.somesite.ru/messaging/" xmlns:ns2="http://www.somesite.ru/data/timetable/delta/" xmlns:ns4="http://www.somesite.ru/datatypes/" xmlns:ns3="http://www.somesite.ru/data/onsi/rail/countries/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">  
   <dataArray recordCount="3">  
     <data xsi:type="ns6:Operator" id="22071" shortName="ИМЯ1" state="active" />  
     <data xsi:type="ns6:Operator" id="22072" shortName="ИМЯ2" state="active" />  
     <data xsi:type="ns6:Operator" id="22073" shortName="ИМЯ3" state="active" />  
   </dataArray>  
 </ns4:Message>]]></transferData>  
      </return>  
    </ns2:processRequestResponse>  
   </soap:Body>  
 </soap:Envelope>  
```

Нам надо:

1. извлечь из transferData/CDATA xml-ку с данными; 
2. посмотреть атрибут recordCount;
3. посмотреть атрибут shortName.

Есть довольно длинный официальный гайд  как провернуть это без использования Groovy-скрипта, используя вместо этого фичу Property Transfer, но по-моему это как-то коряво. 
Мы используем Groovy-скрипт. В шаге теста, где мы запрашиваем сервис данные (GET), добавляем Assertion типа Script > Script Assertion. Используем следующий код (смотри комментарии). Как узнать путь (все эти *//:tranferData* и *@shortName*) описано ниже.

```groovy
 /*Импортируем две нужные библиотеки для работы с XML*/  
 import com.eviware.soapui.support.XmlHolder  
 import com.eviware.soapui.support.GroovyUtils  
 /* Создаем переменную responseXmlHolder, указываем,  
 что содержимое - XML. В переменную записываем возвращенный от сервиса ответ */  
 responseXmlHolder = new XmlHolder(messageExchange.getResponseContentAsXml())  
 /*Из предыдущей переменной с ответом от сервиса извлекаем только данные между tranferData  
 и записываем их в переменную cdataXml.  
 log.info в консоли покажет извлеченные данные */  
 cdataXml = responseXmlHolder.getNodeValue("//*:transferData")  
 log.info (" Извлеченный CDATA: " + cdataXml)  
 /* Еще одна переменная, которая содержит XML данные, на этот раз туда записываем  
 извлеченные из tranferData данные */  
 cdataXmlHolder = new XmlHolder(cdataXml)  
 /* Здесь мы объявляем переменную recordCount, в которую записываем значение,  
 извлеченное из поля <dataArray recordCount="3">. Команда assert == 3 проверяет,  
 что это значение равно 3 (должно быть возвращено 3 записи).*/  
 recordCount = cdataXmlHolder.getNodeValue("//*:dataArray/@recordCount")  
 assert recordCount == "3"   
 log.info (" Количество записей: " + recordCount)  
 /* Узнаем имя записи (указанное в атрибуте shortName поля data.  
 Эта операция выведет имя перевозчика первого узла, а у нас их recordCount=3*/  
 recordName1 = cdataXmlHolder.getNodeValue("//*:dataArray/data/@shortName")  
 log.info ( "Имя 1: " + recordName1)  
 /* А тут мы добавляем указание на конкретное поле data (то, что  
 содержит указанный id, и извлекаем его краткое имя shortName, и проверяем чему оно равно*/  
 recordName2 = cdataXmlHolder.getNodeValue("//*:dataArray/data[@id='22073']/@shortName")  
 assert recordName2 == "ИМЯ2"   
 log.info ( "Имя 2: " + operatorName2)  
```

Как узнать путь до конкретной переменной, что такое вообще путь в XML (XPath)? XML-структуру можно представить как дерево папок:

```
-верхняя папка: <soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
--вложенная папка: <soap:Body>
----следующая вложенная папка: <ns2:processRequestResponse>
------еще одна <return>
--------внутри которой папки <header> и <tranferData>.
```

Можно составить путь до папки самому, а можно использовать замечательный плагин для Notepad++ - XML Tools > Current XML Path (выделив нужный узел, например <trasferData> - поставить туда курсор). Получится что-то вроде: `/soap:Envelope/soap:Body/ns2:processRequestResponse/return/transferData`

Это очень длинный путь со всякими непонятными ns2 и soap (это пространства имен). Нам это все не надо, весь длинный путь мы сокращаем, заменив все символом *. То есть скрипт будет раскрывать все "папки" структуры XML подряд пока не найдет указанную:  `//*:transferData`.

Чтобы сослаться на конкретный атрибут поля data используется указатель @имяатрибута, например, ссылаемся на shortName: `//*:dataArray/data/@shortName`.

А если нужно сослаться на атрибут поля с конкретным атрибутом (например, нужно узнать shortName поля с конкретным Id), то пишем в таком формате: `//*:dataArray/data[@Id='someid']/@shortName`.

Можно сослаться на атрибут уровнем выше, например для XML-ки нужно узнать значение status записи с именем Имя3:

```xml
 <data xsi:type="ns7" status="1" .....>  
       ...  
       <name value="Имя3"/>  
       ...  
 </data>  
```

Будет так: 

`//*:dataArray/data/name[@value='Имя3']/../@status`

<a id="file" />

#### Сериализация файла

Иногда требуется загружать в сервис файл, сериализованный в base64. Рассмотрим пример с простеньким csv файлом.  Для начала поймем, что нужно сделать:

- взять файл, который лежит в какой-то папке. Путь желательно относительный, например в подпапке где лежит файл проекта SoapUI;
- считать содержимое файла (в UTF-8 формате, чтобы не было кракозябр! для русского языка);
- кодировать в base64;
- записать эту кодированную штуку в какую-нибудь переменную;
- вставить переменную в наш запрос Soap.

Так как невозможно все это проделать в одном шаге, у нас будет два шага:

- скрипт Groovy - закодировать файл и записать получившийся base64 в переменную;
- сам запрос - содержит эту самую переменную.

Файл проекта лежит в какой-то папке. Создадим подпапку (например, testfiles), а там папку для тест-кейса, например testcase1.

Вообще-то дерево папок не очень важно, так как мы вынесем путь в свойства, и вы сможете задать свою структуру. Для примера я буду использовать такую:

```
--папка SoapUI <<< тут лежит файл проекта

----testFiles <<< это общая папка для всех тестовых файлов

------testcase1 <<< тут лежит файл file.csv
```

Описание скрипта (сам скрипт см. ниже):

- cначала нам нужно получить путь к файлу проекта (чтобы тест можно было запускать с любой машины и из любой папки). В Custom Properties проекта создадим свойство <testFilesFolder>  и запишем туда путь к тестовым файлам, например `\testFiles\testcase1`
- после этого нам нужно прочитать файл из папки. Мы берем путь к проекту (<projectPath>), прибавляем к нему путь из свойств проекта (<testFilesFolder>) и в конце указываем постоянный путь (''/file.csv'). В результате скрипт прочитает файл, расположенный на компьютере в папке /<путь к папке проекта>/testFiles/testcase1/file.csv.
- у меня запросе SOAP, который отправляет файл, также указывается размер файла. Можно получить размер нашего файла и записать его в переменную <fileSize>(переменная в *Custom Properites* тест-кейса). Затем нужно взять содержимое нашего файла, перевести его в строку UTF-8 (иначе будут кракозябры), кодировать в base64 и сохранить получившуюся строку переменную fileSend в свойствах тест-кейса. (Custom Properties).

```groovy
 import com.eviware.soapui.support.GroovyUtils  
 /*  
  * Получаем относительный путь к папке с тестовыми файлами, testFilesFolder - свойство проекта, где указн путь  
 к папке относительно файла проекта  
 */  
 def testFilesFolder = testRunner.testCase.testSuite.project.getPropertyValue( "testFilesFolder" )  
 log.info(" Путь к папке с тестовыми файлами относительно файла проекта: " + testFilesFolder)  
 def groovyUtils = new com.eviware.soapui.support.GroovyUtils(context)  
 def projectPath = groovyUtils.projectPath  
 log.info(" Путь к папке проекта на диске: "+ projectPath)  
 /*  
  * Читаем файл, расположенный относительно файла проекта в папке, путь к которой  
  * указан в свойствах проекта (в данном случае /testFiles/testcase1/file.csv  
  */  
 def csvFile = new File(projectPath + testFilesFolder, "/file.csv" ).getText("UTF-8");  
 log.info(" Содержимое файла: " + csvFile)  
 /* Определяем размер файла, записываем его в свойста тест-кейса fileSize*/  
 def fileSize = csvFile.length().toString()  
 testRunner.testCase.setPropertyValue( "fileSize", fileSize )  
 /*Берем содержимое файла, пишем в кодированную строку,  
 и записываем ее в Properties этого тест-кейса (Sample)*/  
 /*def csvFileEncoded = csvFile.bytes.encodeBase64().toString()* << кракозябрики*/  
 String csvFileEncoded = csvFile.getBytes( 'UTF-8' ).encodeBase64()
 testRunner.testCase.setPropertyValue( "fileSend", csvFileEncoded )  
```

Теперь эти переменные можно использовать в запросе. Создаем шаг тест-кейса с SoapUI запросом к сервису, которому надо скормить base64, и используем нашу переменную `<fileData>${#TestCase#fileSend}</fileData>`.

<a id="gen-random" />

### Генерация случайного числа (между) или случайной даты (между)

Генерацию случайно числа удобно использовать, например, для рандомных идентификаторов, рандомной даты и т.п. Генерация рандомного числа между указанными значениями (min ... max) выполняется по формуле:
`Math.random()*(max - min))+min`

Например, случайное число между 5 и 10:  `Math.random()*(10 - 5))+5`
Результат: 7.356781231

Округлить (например, число между 1001 и 1140): `Math.round((Math.random()*(1140 - 1001))+1001)`
Результат: 1125

Прямо в теле запроса SOAP можно использовать в таком формате:
`<somexmlfield>${=(int)(Math.round((Math.random()*(1140 - 1001))+1001))} </
somexmlfield>`

Также может пригодится генерация случайной даты. Например, мне это понадобилось для генерации названия файла для загрузки по FTP. Формат имени файла включает год, месяц, день, часы, минуты, секунды и мс. Чтобы было как положено, сгенерируем случайную дату, которая попадает в период между сегодняшней датой (и временем) и датой месяц назад. Это выполняется скриптом Groovy, нужно будет импортировать несколько нужных библиотек. Вот так (читаем комментарии):

```groovy
 /*Импортируем библиотеки*/  
 import java.text.SimpleDateFormat  
 import java.text.DecimalFormat  
 import java.util.Date  
 import java.sql.Timestamp  
 /*Создаем календарь и записываем его в переменную cal  
 * Вторая строчка - записывает текущую дату в указанном в скобках формате (yyyy-MM-dd HH:mm:ss) в переменную currentDate */  
 Calendar cal = Calendar.getInstance()  
 currentDate = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(cal.getTime())  
 /*Теперь отнимаем от текущей даты 30 дней. Если нужно отнять часы, то cal.add(Calendar.HOUR, -24)*/  
 cal.add(Calendar.DATE, -30)  
 /*Записываем получившуюся дату в указанном формате в переменную monthBackDate */  
 monthBackDate = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(cal.getTime())  
 /* Генерируем рандомную дату между сегодня и 30 дней назад  
  * форматируем с указанным форматом: yyyy_MM_dd_HH_mm_ss_SSS  
  * Первая строчка создает новое правило для формата (нам нужно, чтобы дата была с нижними подчеркиваниями)  
  * Вторая и третья строчки задают значение для начальной даты (offset) и конечной (end, которая currentDate).  
  * Четвертая строчка записывает период  
  * Пятая строчка Timestamp генерирует случайную дату в указанном периоде  
  * И наконец дата записывается в нужном нам формате (который был объявлен на первой строчке SimpleDateFormat) в строковую переменную  
  */  
 SimpleDateFormat sdfDate = new SimpleDateFormat("yyyy_MM_dd_HH_mm_ss_SSS")  
 long offset = Timestamp.valueOf(monthBackDate).getTime()  
 long end = Timestamp.valueOf(currentDate).getTime()  
 long diff = end - offset + 1  
 Timestamp rand = new Timestamp(offset + (long)(Math.random() * diff))  
 String cdate = sdfDate.format(rand)  
 log.info(cdate)  
```
<a id="set-get-properties" />

### Установка и получение свойств из проекта или тест кейса

Свойства проекта или тест-кейса (properties) удобно использовать для сохранения и извлечения каких-то общих значений. 

Есть два типа скриптов: скрипт-шаг (*Add Step* - *Groovy Script*) и *script assertion* (скрипт проверка - который добавляется в шаге теста для проверки результатов). У этих скриптов немного разный контекст, поэтому назначение и получение свойств выполняется немного по разному.

Для начала обычный скрипт (*Groovy script*). Вот так получаем значения из свойств проекта:
` def someVar = testRunner.testCase.testSuite.project.getPropertyValue( "propertyName" )`

где <property..> - название свойства проекта, someVar - какая-то переменная

У тест-кейса тоже есть свойства, и иногда удобнее указывать их там. Свойство тест-кейса получаем так:
`def someVar = testRunner.testCase.getPropertyValue( "propertyName" )`

Для того чтобы получить свойство тест-кейса из Script Assertion, нужно действовать немного по другому:

`def testCaseProperty = messageExchange.modelItem.testStep.testCase.setPropertyValue( "propetyName", "Привет, Мир!"  )`

будет создано свойство тест-кейса с именем <propetyName>, со значением Привет, Мир!.

Чтобы получить значение свойства из script assertion:
`def someVar = testRunner.testCase.getPropertyValue( "propertyName" )`

Переменной <someVar> будет назначено значение свойства тест-кейса с <propertyName>.

<a id="paths" />

### Создание, удаление файлов и пути до файлов

В тесте может потребоваться создать какой-то файл в директории. Для этого нам надо: сделать путь до папки относительным (чтобы работало на любом компьютере), создать папку, если ее нет, создать файл.

Чтобы получить путь до файла проекта:

```groovy
def groovyUtils = new com.eviware.soapui.support.GroovyUtils(context)
def projectPath = groovyUtils.projectPath
log.info(" Путь до папки проекта: " + projectPath)
```

Путь к папке с проектом сохраняется в переменную <projectPath>. Мы можем создавать файл прямо в корне папки, тогда файлы будут сохранятся рядом с .xml файлом проекта.

Так как папка может не существовать на момент запуска теста, нужно это проверить, и если папки нету - создать ее:

```groovy
def testDir = new File(projectPath + testFilesFolder)
 if( !testDir.exists() ) {
   testDir.mkdirs()
 }
```

Чтобы создать в этой папке файл нужно также проверить, нет ли там уже его.
В зависимости от особенностей теста, может потребоваться создать новый файл или добавлять записи к существующему.
Код ниже всегда создает новый файл с указанным именем. Если файл уже есть - он удаляется.

```groovy
 def someFile = new File(projectPath + testFilesFolder + "file_generated.csv")
 if (someFile.exists()) {
     someFile.delete();
 }
```

Чтобы добавить в созданный файл какие-то записи, можно использовать команду `append` (будет добавлять строки в конец файла). 
Например, ниже код создает две записи - заголовок файла и одну запись. В конец каждой строки добавляется конец строки (\n)

```groovy
def someHeader = "header1;header2;header3;header4" def someLine1 = "данные1;1234;whatever;45678" someFile.append(someHeader +"\n" + someLine1 + "\n", "UTF-8")
```
<a id="step-activation" />

### Активировать или деактивировать шаги теста по условию

Некоторые шаги тест-кейса иногда требуется активировать или деактивировать в зависимости от условия. Например, если в БД отсутствует запись с каким-то ID, то нам нужно ее создать; а если такая запись есть - то создавать ничего не надо, и шаги с созданием отключаем.

Я, например, делала так:

1. шаг теста с SOAP запросом записи по Id (также можно сделать JDBC запрос, или проделать все это в одном скрипте Groovy);
2. если возвращено 0 записей, то в свойство тест-кейса ставим createRecord=true
3. если возвращена запись - ставим createRecord=false
4. в шаге тест-кейса Groovy Script смотрим значение свойства createRecord, и в зависимости от значения активируем или деактивируем шаги.

Для активации шага теста:

```groovy
def oNameList = testRunner.testCase.getTestStepList().nametestRunner.testCase.getTestStepByName("Имя шага тест-кейса").setDisabled(false)
```

Для деактивации:

```groovy
 def oNameList = testRunner.testCase.getTestStepList().name
 testRunner.testCase.getTestStepByName("Имя шага тест-кейса").setDisabled(true)

```

`getTestStepList` требуется для получения текущего состояния и списка всех шагов тест-кейса.

Например вот так:

```groovy
 /*Получаем список всех шагов теста */  
 def oNameList = testRunner.testCase.getTestStepList().name  
 def createOperator= testRunner.testCase.getPropertyValue( "createRecord" )  
 if (createRecord == "true") {  
   log.info(' Нету записи с таким id, создаем')  
   testRunner.testCase.getTestStepByName("Create Record").setDisabled(false)  
   testRunner.testCase.getTestStepByName("Check Record").setDisabled(false)  
   testRunner.testCase.getTestStepByName("Remove Record DB").setDisabled(false)  
 }  
 else if (createRecord == "false") {  
   log.info(' Запись с таким id уже есть, используем его, деактивируем ненужные шаги')  
   testRunner.testCase.getTestStepByName("Create Record").setDisabled(true)  
   testRunner.testCase.getTestStepByName("Check Record").setDisabled(true)  
   testRunner.testCase.getTestStepByName("Remove Record DB").setDisabled(true)  
 }  
 else {  
   log.info(' Так быть не должно')  
 }  

```
