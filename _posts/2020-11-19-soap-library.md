---
title: SoapUI - разработка библиотеки для чайников
toc: true
toc_label: "Содержание"
tags:
  - soapui
  - ru
categories:
  - qa
---

Понадобилось мне тут опять покопаться в SoapUI. Ситуация нынче напряженная, тестировщики все в бегах, так что приходится браться за лопату техническому писателю. Бесплатная версия SoapUI и так много чего умеет, однако не хватает ей одной важной фичи - создание собственных "библиотек" скриптов. Платная версия позволяет сохранять часто-используемые скрипты в папочку в виде "классов", а затем вызывать в их в практически в любых скриптах. 

В [этой статье](2018-12-25-SoapUI-Tips-And-Tricks.md) я уже писала про приключения в SoapUI для начинающих.  Скрипты это хорошо, но что делать, если какие-то конструкции нужно использовать вот прям постоянно и писать одно и то же достало? Можно вынести часть часто используемых скриптов в библиотеку и вызывать нужные методы, когда они понадобятся. SoapUI Pro поддерживает такую фичу из коробки, прямо из интерфейса пользователя, но пользователям SoapUI Opensource придется идти путем просветления: 

- написать скрипты и скомпилировать их в jar, 
- а затем скопировать в папку `<SoapUI %INSTALLDIR%>/bin/ext`.

Есть ещё способ импортирования скриптов и общего проекта, но этот способ уж слишком костыльный даже для меня. 

## Подготовка

Нам понадобятся:

* среда разработки (например, [Idea Intellij](https://www.jetbrains.com/idea/download/), хватит community версии);
* [Groovy](https://www.groovy-lang.org/) (распаковать, добавить в переменные окружения своей ОС);
* [gradle](https://gradle.org/), который нам поможет все собрать в .jar файл;
* некоторый опыт в разработке скриптов и навыки гуглирования... гугления?

Установите Idea, установите Gradle (загрузить и распаковать) и добавьте gradle в переменные окружения ([вот тут написано как](https://stackoverflow.com/a/37774085). В Idea в **Settings** > **Build, Execution, Deployment** > Build Tools > ... убедиться, что путь к gradle подцепился).

Создать в Idea новый проект. В процессе создания выбрать Gradle и указать `artifact id` например `soapui.library` (`groupid` можно оставить пустым). Gradle создаст структуру папок и выполнит начальную настройку. Вот такая структура папок, например, вышла у меня:

![image-20210701145241365](/assets/images/posts/2020-11-19-sl-01.png)

Чтобы использовать некоторые классы SoapUI (например для установки свойств тест-кейса) нужно импортировать библиотеку SoapUI. Ее можно скопировать прямо из установленного `%installdir%SoapUi/bin/`, файл `soapui-5.5.0.jar` (версия может отличаться) , скопировать в папку `/libs` в директории проекта (создать папку). Чтобы подключить библиотеку нужно в файл `build.gradle` в корне проекта отредактировать раздел `dependencies`, а также добавить информацию о .jar. Вот так получилось у меня:

```
plugins {
    id 'groovy'
    id 'java'
}

version '1.0'

jar {
    manifest {
        attributes 'Implementation-Title': 'SoapUI Sample Groovy Library'
    }
}

sourceCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
    compile 'org.codehaus.groovy:groovy-all:2.3.11'
    compile group: 'org.apache.xmlbeans', name: 'xmlbeans', version: '3.1.0'
    compile fileTree(dir: 'libs', include: '*.jar')
    testCompile group:'org.spockframework', name: 'spock-core', version: '1.1-groovy-2.3'
    testCompile group: 'junit', name: 'junit', version: '4.12'
}
```

Как видно, тут я включила еще парочку подозрительных библиотек. Библиотека *xmlbeans* нужна SoapUI для работы с XML, а библиотеку Spock (org.spockframework) можно использовать для тестирования скриптов.

Классы объединим в `package ru.company.soapui` (где company – название компании, в моем случае). Это нужно для того, чтобы в SoapUI импортировать скрипты из библиотеки, вот так, например: `import ru.inforion.soapui.*`, ну и чтобы с другими пакетами, коих тысячи, не пересекались названия методов и классов.

## Кодим!

В папке `/main/groovy` создадим `package` (правой кнопкой - package) и обозвать его, например, `ru.inforion.soapui`. Кликнуть ПКМ по package и создать Groovy Class. Idea очень умная, так что она сразу создаст каркас для класса, осталось только вставить методы. Можно создавать несколько классов для скриптов разного назначения, а можно запихать все в один, но есть пара нюансов.

В SoapUI для выполнения скриптов используются:
* `testRunner` – в Test Step;
* `messageExchange` – в Script Assertion (например, при разборе ответа от сервиса).

Сравните, например, установку свойств в Test Step и Script Assertion:

```groovy
testRunner.testCase.testSuite.project.setPropertyValue( "projectProperty" ,"Значение свойства")
messageExchange.modelItem.testStep.testCase.testSuite.project.setPropertyValue( "projectProperty" ,"Значение свойства")
```

Чтобы запускать один и тот же скрипт в разных контекстах, нужно будет немножко извернуться. 

Например:

```groovy
package ru.inforion.soapui
public class Utils {
    // этот блок обязателен, чтобы скрипт выполнился в SoapUI
    def context 
    def log
    def runner
    
    def sortString(String input) {
        char[] charArray = input.replaceAll("[^а-яА-Яa-zA-Z0-9 ]+","").toCharArray()
        Arrays.sort(charArray)
        String sortedString = new String(charArray)
    
        return sortedString.trim()
    }
}
```

Этот класс можно сразу скомпилировать и протестировать. Для этого в окне Idea справа (такая маленькая вертикальная панелька) раскрыть панель Gradle, раскрыть *Tasks/build* и дважды кликнуть *jar*. Проект скомпилируется, готовый `.jar` будет в папке проекта в директории `/build/libs` (по умолчанию). Этот `.jar` необходимо скопировать в папку `C:\Program Files (x86)\SmartBear\SoapUI-x.x.x\bin\ext` и перезапустить SoapUI.

В Groovy Script вызывать класс и метод нужно вот так:

```Groovy
import ru.inforion.soapui.*

def myClass = new Utils() //в данном случае нам не нужен ни testRunner, ни messageExchange, так что ничего в скобках не указываем
log.info (myClass.sortString("Ура работает!"))
```

Для того чтобы использовать объекты SoapUI, например устанавливать свойства тест-кейса или работать с XML ответом от сервиса, нужно указать контекст. Например, метод для сериализации строки в base64 и сохранения результата в свойство тест-кейса, который можно запускать и из Test Step, и из Script Assertion:

```Groovy
package ru.inforion.soapui

class Utils {
    def context
    def log
    def testRunner

/**
* Переводит в base64 строку и сохраняет в указанное свойство Тест-кейса.* 
* Метод работает и в тест-степе, и в Script Assertion
*
* @param prop Имя свойства для сохранения base64.
* @param input Строка (строки) для кодирования.
*/
def base64This(String prop, String input) {	
	String encoded = input.getBytes( 'UTF-8' ).encodeBase64()
	//проверяем, если скрипт запущен из Test Step, то используем конструкцию с testRunner
    if (runner.getClass().getSimpleName() == "MockTestRunner") {
		runner.testCase.setPropertyValue("${prop}",encoded)
	}
	//или messageExchange для ScriptAssertion
	else {
		runner.modelItem.testStep.testCase.setPropertyValue("$prop",encoded)
	}        
}
```

Вызвать этот класс и метод можно вот так:
Из Test Step:

```Groovy
import ru.inforion.soapui.*
//для runner указываем testRunner
def anotherClass = newUtils(context: context, log: log, runner: testRunner)
anotherClass.base64This("Property","Blabla")
```

Из Script Assertion:

```Groovy
import ru.inforion.soapui.*
//для runner указываем messageExchange

def anotherClass = newUtils(context: context, log: log, runner: messageExchange)
anotherClass.base64This("Property","Blabla")
```

Можно (ладно, нужно, чтобы на вас коллеги-разработчики не смотрели косо) добавить обработку ошибок. Например, ниже метод для сортировки символов строки, в котором ловится ошибка, если не указана строка для сортировки (больше примеров с try-catch [тут](https://www.softwaretestinghelp.com/soapui-tutorial-11-exception-handling-in-soapui-groovy-scripts/)).

```Groovy
def sortString(String input) {
        try {
            char[] charArray = input.replaceAll("[^а-яА-Яa-zA-Z0-9 ]+","").toCharArray()
            Arrays.sort(charArray)
            String sortedString = new String(charArray).trim()

            return sortedString.trim()
        }
        catch (NullPointerException e) {
            log.error (" Не указана строка для сортировки ", e)
            assert false //кидаем ошибку в soap. Если эта ошибка поймана, тест можо остановить
        }
    }
```


Блок try защищает от ошибок код внутри `{ }`. Также можно отловить все Exception без указания конкретного (`catch (Exception e) {log.error (e)}`), но это считается плохой практикой.

## Тестирование

Часть методов можно проверить прямо в Idea. Idea уже создала папку `/test` (рядом с `/main`), в которой можно создать класс, например UtilsTest, для тестирования. Для тестирования groovy проще всего использовать фреймворк [Spock](http://spockframework.org/spock/docs/1.3/index.html).

Например, класс и метод для тестирования sortString из примера выше:

```groovy
import spock.lang.Specification
import ru.inforion.soapui.*

class UtilsTest extends Specification {
    def tc = new Utils()

    def "String sorting"() {
       given: 
            String str = tc.sortString("Некоторая строчка из different 4562 символов")
       expect:
            str == "2456deeffinrtНааввезиикклмоооооррссттчя"
   }
}
```

Spock хорош тем, что в нем все наглядно. Given - дано, expect - ожидаемый результат. 
Можно сразу запустить тест (зеленой кнопочкой слева от кода).

![image-20210720133252334](/assets/images/posts/2020-11-19-sl-02.png)

