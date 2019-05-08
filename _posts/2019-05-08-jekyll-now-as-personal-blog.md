---
layout: post
title: jekyll-now для персонального блога
tags: docops
---

Небольшой гайд для чайников про запуск своего блога на [GitHub Pages](https://pages.github.com/), несколько полезных твиков для добавления в блог архива, тегов и новых иконок.

Для ведения небольшого (*хотя большой тоже можно*) личного блога с заметками и статьями отлично походят движки статических сайтов или *flat-file CMS* ([Jekyll](https://jekyllrb.com/), [Grav CMS](https://getgrav.org/), [Kirby](https://getkirby.com/) и тысячи их). Преимуществом **Jekyll** является то, что его из коробки поддерживает [GitHub Pages](https://pages.github.com/). Это значит, что не нужно возиться с установкой ПО, запускать веб-серверы и пр, достаточно создать аккаунт [GitHub](https://github.com/), создать репозиторий для сайта, закинуть туда файлы сайта (или форкнуть репозиторий) ... и все! Сами посты блога можно писать в *Markdown* в любом удобном, любимом редакторе или прямо через веб-интерфейс GitHub.

**Содержание:**

- <a href="#github">Создаем аккаунт и форкаем репозиторий</a>
- <a href="#jekyll-struct">Структура jekyll-now</a>
- <a href="#config">Конфигурация</a>
- <a href="#posting">Пишем пост</a>
- <a href="#addicon">Добавляем иконки</a>
- <a href="#archive">Добавляем страницу со списком постов</a>
- <a href="#tags">Добавляем теги и облако тегов</a>
- <a href="#fixcode">Исправляем баг с подсветкой кода</a>
- <a href="#git">Ставим git для Windows</a>
- <a href="#plugins">Плагины</a>

<a id="github" />

### Создаем аккаунт и форкаем репозиторий

Нам нужно:
1. зарегистрировать аккаунт на [github.com](https://github.com/). Видео на русском языке, как создавать аккаунт, например, [вот это](https://youtu.be/9dkzbSnN2FQ?t=41), спасибо `@ITDoctor`;
2. форкнуть репозиторий [jekyll-now](https://github.com/barryclark/jekyll-now) (или мой репозиторий). В этом репозитории самый простой базовый блог, который дальше можно редактировать. В нем есть парочка другая багов, но их нетрудно исправить. Чтобы форкнуть репозиторий (см. [короткую анимацию](https://raw.githubusercontent.com/barryclark/jekyll-now/master/images/step1.gif) из репозитория автора jekyll-now):
   3.  нужно в верхнем правом углу (где количество просмотров и звезд) нажать кнопку *Fork*;
   2. дождаться пока репозиторий скопируется в ваш аккаунт;
   3. открыть репозиторий в вашем аккаунте и перейти на вкладку *Settings*;
   4. поменять имя (*Repository name*) на yourgithubusername.github.io, где yourgithubusername - ваше имя на GitHub (это обязательный формат, иначе сайт автоматически работать не будет). 
4. сайт с блогом будет доступен по ссылке https://yourgithubusername.github.io, где yourgithubusername - имя вашего аккаунта github. GitHub Pages ребилдит сайт каждый 10 мин. или при изменении файла `_config.yml`.

Ура, треть дела сделана. 

<a id="jekyll-struct" />

### Структура jekyll-now

Теперь в репозитории лежит набор файлов и папок. Поясню кратко для чего каждый из них. Подробнее можно узнать в [документации Jekyll](https://jekyllrb.com/docs/structure/) и вот в этом [гайде](https://www.smashingmagazine.com/2014/08/build-blog-jekyll-github-pages/).

| Файл или папка | Для чего нужен                                               |
| -------------- | ------------------------------------------------------------ |
| `_config.yml`  | основные настройки, название блога,  ссылки на аккаунты социальных сетей ([образец](https://github.com/barryclark/jekyll-now/blob/master/_config.yml)) |
| `style.css`    | основной стилевой файл CSS                                   |
| `index.html`   | домашняя страница. Файлы в корне репозитория (например, `about.md`, `archive.html`) также обрабатываются jekyll. |
| `/_layouts`    | шаблоны для основных страниц блога (`default.html`, `page.html`,`post.html`) |
| `/_posts`      | здесь хранятся записи блока в формате markdown. Имя файла должны быть в формате: YYYY-MM-DD-any-post-title.md |
| `/_includes`   | небольшие файлы, содержащие скрипты в формате [Liquid](https://shopify.github.io/liquid/) |
| `/_sass`       | содержит дополнительные стилевые файлы, написанные на метаязыке [Saas](https://sass-scss.ru/) |

<a id="config" />

### Конфигурация

В файле `_config.yml` меняем 

- `name: Your Blog Name` - название вашего блога
- `description: Your Description Here` - краткое название блога (отображается под названием)
- `avatar: https://link` - укажите ссылку на ваш логотип/фото. Можно загрузить картинку в папку images в репозитории и ссылка тогда будет вида <https://raw.githubusercontent.com/annjulyleon/yourgithubusername.github.io/master/images/jekyll-logo.png>. Аватарка размером 40x40 px по умолчанию
- в секции `footer-links` указываем username или ссылки на аккаунты в соответствующих соцсетях. Иконки с пустыми значениями отображаться не будут. Формат URL (как именно нужно указывать urename) можно посмотреть в файле `_includes/svg-icons.html`
- `url: https://yourgithubusername.github.io` - укажите URL вашего блога
- `baseurl: /repository-name` - если вы хотите запускать блог из репозитория проекта http://yourusername.github.io/repository-name, а не репозитория пользователя (просто http://yourusername.github.io), то тут нужно указать название репозитория - `/repository-name`

<a id="posting" />

### Пишем пост

Чтобы добавить пост, создаем файл в формате .md с указанием даты и названия. Дата в названии файла должна быть обязательно, латиница, без пробелов:

`YYYY-MM-DD-any-post-title.md`

`2019-04-24-post-title.md`

В каждом посте в начале файла должна быть начальная секция ([в формате YAML](https://jekyllrb.com/docs/front-matter/)), где минимум указываем layout: (один из шаблонов, например page или post) и title: (название, как оно будет отображаться в списке постов и на главной странице). Например:

```markdown
---
layout: post
title: jekyll-now для персонального блога
---

Небольшой гайд для чайников про запуск своего блога на [GitHub Pages](https://pages.github.com/), несколько полезных твиков для добавления в блог архива, тегов и новых иконок.
```



Текст поста набран на языке разметки markdown (см. [шпаргалку на русском](https://github.com/sandino/Markdown-Cheatsheet)). Можно редактировать текст прямо в интерфейсе GitHub или сначала писать их с помощью любого Markdown редактора, например [Typora](https://typora.io/), [Joplin](https://joplinapp.org/), [плагины для Atom](https://atom.io/packages/markdown-preview) и [IDEA](https://plugins.jetbrains.com/plugin/7793-markdown-support) и тысячи их.

<a id="addicon" />

### Добавляем иконки

Можно добавлять нужные иконки соцсетей в формате .svg. Например, я добавила иконки для Telegram и Blogger. Есть официальный [гайд](https://github.com/barryclark/jekyll-now/wiki/Adding-Icons) на английском языке, привожу его здесь:

- найдите иконку, которая вам нужна, в формате SVG. Например, можно посмотреть [тут](https://github.com/neilorangepeel/Free-Social-Icons/tree/master/Flat/SVG);

- скопировать XML-исходник SVG иконки (открыть в блокноте скачанный SVG файл или нажать кнопку Raw при просмотреть файла SVG в репозитории);

- нужно сериализовать XML в base64. Для этого можно использовать Notepad++ или онлайн конвертер [mobilefish.com](https://www.mobilefish.com/services/base64/base64.php). Вставьте XML текст в поле `Enter source data*:`, укажите значение 0 в поле `Max characters per line:` (нам не нужно разбивать строки), введите access code с картинке и нажмите Convert;

- скопируйте сериализованный текст. В файле sass/_svg-icons.scss добавьте строку вида:

```
&.SOMENAME { background-image: url(data:image/svg+xml;base64,<your encoded string here>); }
```

SOMENAME - название вашей иконки/соцсети. Вставьте сериализованный текст вместо <your encoded string here>.

Например, [здесь](https://github.com/annjulyleon/annjulyleon.github.io/blob/master/_sass/_svg-icons.scss) две последние строки - для добавления иконок blogger.com и Telegram.

Теперь, чтобы отобразить иконки на страницах блога, нужно отредактировать файл `_includes/svg-icons.html`, добавив в него строки вида:

```
{% if site.footer-links.SOMENAME %}<a href="your LINK here/{{ site.footer-links.SOMENAME }}"><i class="svg-icon SOMENAME"></i></a>{% endif %}
```

где SOMENAME - имя иконки, которое вы назначили на предыдущем шаге, а <your LINK here> - базовая ссылка (t.me для телеграм, blogger.com и т.д.). См. [пример](<https://github.com/annjulyleon/annjulyleon.github.io/blob/master/_includes/svg-icons.html>).

И наконец, добавим параметр конфигурации для добавленной иконки в `_config.yml`:

```yml
footer-links:
SOMENAME: /yourname
```

<a id="archive" />

### Добавляем страницу со списком постов

Нужно добавить страницу со списком постов (например, [вот такую](https://annjulyleon.github.io/archive/)) по годам с датами. Вот [гайд](http://chris.house/blog/building-a-simple-archive-page-with-jekyll/) на английском языке.

Добавьте в корень репозитория файл archive.html и скопируйте содержимое:

```markdown
---
layout: page
title: Archive
permalink: /archive/
---

<section class="archive-post-list">
{% for post in site.posts %}
       {% assign currentDate = post.date | date: "%Y" %}
       {% if currentDate != myDate %}
           {% unless forloop.first %}</ul>{% endunless %}
           <h1>{{ currentDate }}</h1>
           <ul>
           {% assign myDate = currentDate %}
       {% endif %}
       <li><a href="{{ post.url }}"><span>{{ post.date | date: "%B %-d, %Y" }}</span> - {{ post.title }}</a></li>
       {% if forloop.last %}</ul>{% endif %}
   {% endfor %}
</section>
```

Затем нужно добавить эту страницу в навигацию блога. Для этого откройте файл `/_layouts/default.html` и найдите секцию <nav> . Добавить страницу:

```xml
 <nav>
    <a href="{{ site.baseurl }}/">Блог</a>
    <a href="{{ site.baseurl }}/about">Обо мне</a>
    <a href="{{ site.baseurl }}/archive">Архив</a>
</nav>
```

Здесь же можно переименовать название пунктов меню.

Страница будет доступна в течение 10 мин.

<a id="tags" />

### Добавляем теги и облако тегов

Чтобы проще было ориентироваться в блоге и находить похожие статьи, можно добавить в блог теги. Есть несколько способов реализации тегов и категорий (например, [вот](https://codinfox.github.io/dev/2015/03/06/use-tags-and-categories-in-your-jekyll-based-github-pages/) с коллекциями), я выбрала самый простой (взяла [отсюда](https://dev.to/rpalo/jekyll-tags-the-easy-way)). 

Добавьте к каждому своему посту в начальную секцию (*front matter*) теги через пробел или запятую. Например:

```yaml
---
layout: post
title: jekyll-now для персонального блога
tags: docops jekyll 
---
```



Затем нужно вывести теги для каждого поста на странице. Мне нравится выводить их перед текстом, сразу после даты, кому-то больше нравится в конце, после поста. В файл `/_layouts/post.html/` нужно в начало или конец файла добавить секцию с тегами (секция <small> ниже). Я добавила в секцию с отображением даты (прямо перед <div class="entry">):

```html
<div class="date">
    Written on {{ page.date | date: "%B %e, %Y" }}
	<br />
   <small>
     {% for tag in page.tags %}
     <a href="/tags/{{ tag }}/">{{ tag }}</a>
     {% endfor %}
  </small>
</div> 
```

Теперь нужно создать страницы для каждого тега. на каждой страницы будут перечислены все посты с тегом. Создадим файл `_layouts/tagpage.html` и добавим содержимое:

```html
---
layout: page # This template inherits from my basic page template
---

<!-- We're going to give each page a tag when we create it.
This will be the title -->
<h1>{{ page.tag | capitalize }}</h1>

<ul>
    {% for post in site.tags[page.tag] %}
    <li>
        {{ post.date | date: "%B %d, %Y" }}: <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
    {% endfor %}
</ul>
```



Теперь создадим папку `/tags` в репозитории и для каждого тега создадим файл `<имя тега>.md`. В файле только начальная секция с именем тега:

 ```markdown
---
layout: tagpage
tag: jekyll
---
 ```

Готово!

Теперь добавим на главную страницу облако тегов. Для этого отредактируем index.html, добавив в начало или в конец (как вам больше нравится) следующую секцию:

```html
<p>
    {% for tag in site.tags %}
    <!-- Here's a hack to generate a "tag cloud" where the size of
    the word is directly proportional to the number of posts with
    that tag. -->
    <a href="/tags/{{ tag[0] }}/" 
    style="font-size: {{ tag[1] | size | times: 2 | plus: 10 }}px">
        {{ tag[0] }} | 
    </a>
    {% endfor %}
</p>
```

<a id="fixcode" />

### Исправляем баг с подсветкой кода

В умолчальном jekyll-now есть досадный баг: фреймы с кодом дублируются (один внутри другого). Решений этой проблемы несколько (например, [тут](https://github.com/barryclark/jekyll-now/issues/1223)). 

В файле `_sass/_highlights.scss` измените строчку `.highlight` на `pre.highlight`. Также измените строчку `overflow: scroll;` на `overflow: auto;`, чтобы полосы прокрутки отображались, только если это необходимо.

<a id="git" />

### Ставим git для Windows

Все указанные выше действия можно выполнить в интерфейсе GitHub, однако это не всегда удобно. Можно установить на компьютер Git и редактировать файлы в вашем любимом редакторе. 

[Вот отличный гайд](http://www.greendocs.ru/git/) с картинками на русском языке по установке и работе с Git и TortoiseGit.

Для Windows нам нужно установить сначала [Git](https://git-scm.com/), потом [TortoiseGit](https://tortoisegit.org/). После установки команды Git появятся в контекстном меню проводника. 

Создайте папку для ваших исходников, откройте ее и кликните по пустому месту ПКМ. Выберите *Git Clone...*. В появившемся окне в поле URL скопируйте ссылку на ваш репозиторий и нажмите ОК. Вот где можно взять эту ссылку:

![](https://raw.githubusercontent.com/annjulyleon/annjulyleon.github.io/master/images/git-clone.jpg)



Файлы и репозитория склонируются в вашу папку. 

Теперь, если вы внесли в какой-то файл или файлы изменения, то необходимо также кликнуть правой кнопкой в любом месте папки и нажать *Git commit > master*. Ввести краткое описание изменений и нажать *Commit*, а затем *ПКМ* > *Tortoise Git* > *Push* или *Git Sync*. Локальные изменения попадут в ваш репозиторий на GitHub.

Чтобы добавить новый файл в репозиторий, нужно кликнуть по нему или по пустому месту в репозитории и выбрать команду *Add* (или *TortoiseGit > Add* ). Затем также выполнить *Commit* и *Push* для загрузки файлов в удаленный репозиторий.

<a id="plugins" />

### Плагины

Для jekyll есть множество полезных плагинов, но к несчастью самые классные из них GitHub Pages не поддерживаются. 

[Вот](https://help.github.com/en/articles/adding-jekyll-plugins-to-a-github-pages-site) список тех плагинов, которые GitHub Pages поддерживает. Чтобы подключить плагин, нужно отредактировать файл `_config.yml`, добавив в него секцию `plugins:` со список нужных плагинов, например:

```
plugins:
 - jekyll-feed
 - jekyll-redirect-from
 - jekyll-seo-tag
 - jekyll-sitemap
 - jekyll-avatar
 - jemoji
 - jekyll-mentions
 - jekyll-include-cache
```

Интересные статьи:

- [Продвинутый Jekyll](https://habr.com/ru/post/336266/)
- Очень симпатичный [шаблон](https://github.com/Huxpro/huxpro.github.io) с интересными решениями