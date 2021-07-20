---
title: Yet another one Minimal Mistakes setup guide
toc: true
toc_label: "Table of Contents"
tags:
  - jekyll
  - md
  - en
categories:
  - guides
---

[Jekyll Now was cool](2019-05-08-jekyll-now-as-personal-blog.md), but it's time to move forward. One of the best themes for Jekyll is [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/) by [Michael Rose](https://github.com/mmistakes). It's easy to install and customize and it's compatible with GitHub Pages. Moreover it has great documentation to learn basic Jekyll stuff, and I just love projects with great documentation!

## Setup developer environment

There was a time when I was stupid enough to develop right on the prod, flooding my github pages repo with stupid small commits. What can I say, I was young and haven't followed the best practices as close as should have. Today WSL/WSL2 Windows 10 feature or docker offers an easy way to setup developer environment for Jekyll. 

1. First, [setup WSL2](https://docs.microsoft.com/en-us/windows/wsl/install-win10#manual-installation-steps). 

2. Install [Ubuntu 20.04](https://www.microsoft.com/en-us/p/ubuntu-2004-lts/9n6svws3rx71) (or any last version which is available in Microsoft Store).

3. Upgrade with `apt get update && upgrade`.

4. [Install Jekyll](https://jekyllrb.com/docs/installation/ubuntu/) environment:

    ```bash
    sudo apt-get install ruby-full build-essential zlib1g-dev
    
    echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
    echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
    echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
    source ~/.bashrc
    
    gem install jekyll bundler
    ```

   

5. `cd` to your working directory and Initialize you site directory:

    ```
    jekyll new myblog
    ```

6. Edit your `Gemfile` to setup theme Minimal Mistakes and include all necessary extensions:

    ```
    source "https://rubygems.org"
    gem "jekyll", "~> 4.2.0"
    gem "minimal-mistakes-jekyll"
    
    group :jekyll_plugins do
      gem "jekyll-feed", "~> 0.12"
      gem "jekyll-paginate"
      gem "jekyll-sitemap"
      gem "jekyll-gist"
      gem "jemoji"
      gem "jekyll-include-cache"
      gem "jekyll-algolia"
    end
    
    platforms :mingw, :x64_mingw, :mswin, :jruby do
      gem "tzinfo", "~> 1.2"
      gem "tzinfo-data"
    end
    gem "wdm", "~> 0.1.1", :platforms => [:mingw, :x64_mingw, :mswin]
    ```
   
7. Edit you `_config.yml` file. Take [this](https://github.com/mmistakes/minimal-mistakes/blob/master/_config.yml) or [this](https://github.com/mmistakes/mm-github-pages-starter/blob/master/_config.yml) configs as example:

    ```yaml
    title                 : Your awesome blog
    email                 : 
    description: >- 
      Some description.
    baseurl               : ""
    url                   : http://yourname.github.io
    github_username       : annjulyleon
    
    theme                 : minimal-mistakes-jekyll
    minimal_mistakes_skin : default
    search                : true
    words_per_minute      : 200
    
    markdown              : kramdown
    highlighter           : rouge
    permalink             : /:categories/:title/
    paginate              : 5
    paginate_path         : /page:num/
    timezone              : Europe/Moscow
    
    teaser: /assets/images/default/teaserfallback.png
    logo: /assets/images/default/logo.png
    
    include:
      - _pages
      
    plugins:
      - jekyll-feed
      - jekyll-paginate
      - jekyll-sitemap
      - jekyll-gist
      - jekyll-feed
      - jemoji
      - jekyll-include-cache
    
    author:
      name                : "Your Name"
      avatar              : "/assets/images/default/bio-photo.jpg"
      bio                 : "That's me!"
      location            : "Moscow, Russia"
      links:
        - label: "GitHub"
          icon: "fab fa-fw fa-github"
          url: "https://github.com/yourname"
        - label: "LinkdIn"
          icon: "fab fa-fw fa-linkedin"
          url: "https://www.linkedin.com/in/yourname/"
    
    footer:
      links:
        - label: "GH"
          icon: "fab fa-fw fa-github"
          url: "https://github.com/yourname"
        - label: "LI"
          icon: "fab fa-fw fa-linkedin"
          url: "https://www.linkedin.com/in/yourname/"
    
    encoding             : "utf-8"
     
    category_archive:
      type: liquid
      path: /categories/
    tag_archive:
      type: liquid
      path: /tags/
    
    defaults:
      - scope:
          path: ""
          type: posts
        values:
          layout: single
          author_profile: true
          read_time: true
          related: true
          show_date: true
      - scope:
          path: "_pages"
          type: pages
        values:
          layout: single
          author_profile: true
    ```
8. Save file and install:

    ```
    bundle
    bundle update
    ```

   

9. That\'s it! You are ready to start developing. When you are done customizing your awesome site run the command:

    ```
    bundle exec jekyll serve
    ```

   

Site will be available on http://127.0.0.1:4000/ by default.

All theme directories, files and layouts are stored inside gem, so your site folder will appear to be pretty empty. If you want to overwrite something, let's say layout, you can copy file from gem directory. Show theme folder:

```
bundle info minimal-mistakes-jekyll

  * minimal-mistakes-jekyll (4.23.0)
        Summary: A flexible two-column Jekyll theme.
        Homepage: https://github.com/mmistakes/minimal-mistakes
        Path: /home/linuser/gems/gems/minimal-mistakes-jekyll-4.23.0
```

Copy file from gem to site folder:

```
cp /home/linuser/gems/gems/minimal-mistakes-jekyll-4.23.0/_includes/author-profile.html _includes/
```



## Little tricks

There are tons of things you can do with minimal mistakes theme. You can set up gallery, configure overlay images, table of contents and so on. 

### Add favicon

When you build and start your Jekyll site, you\'ll get error like this:

```
ERROR `/favicon.ico' not found.
```

So we need to create favicon for our site and enable them via includes. I used mentioned in the [source article](https://ptc-it.de/add-favicon-to-mm-jekyll-site/) [Real Favicon generator](https://realfavicongenerator.net/). You need to upload an image (png or svg) to generate a favicon. I used [Ucraft Logo maker](https://www.ucraft.com/free-logo-maker) to create simple logo (you don't need to pay, just make a screenshot of your logo and remove background).

There are tons of setting available to tune your favicon and icons, but most important setting is the **Favicon Generator Options** > **Path**. Select `I cannot or do not want...` radio button and specify your path in the `/assets/images/` folder. Hit **Generate your Favicons and HTML code** button. You can download generated resources in zip archive and copy the HTML you need to add in `<head>` section of your site.

![2021-06-25-mm-02](/assets/images/posts/2021-06-25-mm-02.png)

Now we need a copy of `custom.html` for our site. 

1. Make a directory `/_includes/head/ `.
2. Remember the location of the minimal mistakes gem?
   
    ```
    bundle info minimal-mistakes-jekyll
  * minimal-mistakes-jekyll (4.23.0)
        Summary: A flexible two-column Jekyll theme.
        Homepage: https://github.com/mmistakes/minimal-mistakes
        Path: /home/linuser/gems/gems/minimal-mistakes-jekyll-4.23.0
      
    
    ```
3. Copy `custom.html` file to your directory:

    ```
    cp /path/to/gems/minimal-mistakes-jekyll-4.23.0/_includes/head/custom.html _includes/head
    ```

4. Now edit you `custom.html` file. Copy and paste `HTML` code from the [realfavicongenerator.net](https://realfavicongenerator.net/).

    ~~~~~ html
    {% raw %}
    <!-- start custom head snippets -->
    
    <!-- insert favicons. use https://realfavicongenerator.net/ -->
    
    <link rel="apple-touch-icon" sizes="180x180" href="/assets/images/default/apple-touch-icon.png">
    <link rel="icon" type="image/png" sizes="32x32" href="/assets/images/default/favicon-32x32.png">
    <link rel="icon" type="image/png" sizes="16x16" href="/assets/images/default/favicon-16x16.png">
    <link rel="manifest" href="/assets/images/default/site.webmanifest">
    <link rel="mask-icon" href="/assets/images/default/safari-pinned-tab.svg" color="#5bbad5">
    <meta name="msapplication-TileColor" content="#2b5797">
    <meta name="theme-color" content="#ffffff">
    
    <!-- end custom head snippets -->
    {% endraw %}    
    ~~~~~

Now, build your site and watch favicon shines!

Source: [Adding a favicon to a Jekyll-based static website](https://ptc-it.de/add-favicon-to-mm-jekyll-site/) by [@paultcochrane](https://github.com/paultcochrane/)

### Setup galleries and exclude categories from home

Gallery can be added to any post or page. Official documentation for galleries is available [here](https://mmistakes.github.io/minimal-mistakes/docs/helpers/#gallery). 

You might want to exclude posts of certain category from home page (like I don't want galleries rendered in home page post list). You can use [Collections](https://jekyllrb.com/docs/collections/) or edit Liquid template rules for home page.

Copy `home.html` page from the gem:

```
cp /home/<user>/gems/gems/minimal-mistakes-jekyll-4.23.0/_layouts/home.html _layouts/
```

Edit the render rule to exclude certain category with `unless`:

```html
{% raw %}
{% for post in posts %}
  {% unless post.categories contains 'gallery' %}
    {% include archive-single.html type=entries_layout %}
  {% endunless %}
{% endfor %}
{% endraw %}
```



### Load custom css and js

Useful trick to load small js and css files per page. Source: [Setting Per-Page Custom CSS in Jekyll](https://jreel.github.io/per-page-custom-css-in-jekyll/) by [@jreel](jreel.github.io).

1. Copy `default` layout from the gem. Example command:

    ```
    cp /home/<user>/gems/gems/minimal-mistakes-jekyll-4.23.0/_layouts/default.html _layouts/
    ```

2. Open `default.html` in you favorite text editor and add these lines into the `<head>` section, after all other includes:

    ~~~~~ html
    {% raw %}
    {% if page.custom_css %}
      {% for stylesheet in page.custom_css %}
         <link rel="stylesheet" href="{{ site.baseurl }}/assets/css/{{ stylesheet }}.css">
      {% endfor %}
    {% endif %}
    {% endraw %}
    ~~~~~

    So `<head>` section looks like this:

    ~~~~~ html
    {% raw %}
    <head>
        {% include head.html %}
        {% include head/custom.html %}
        {% if page.custom_css %}
          {% for stylesheet in page.custom_css %}
            <link rel="stylesheet" href="{{ site.baseurl }}/assets/css/{{ stylesheet }}.css">
          {% endfor %}
        {% endif %}
    </head>
    {% endraw %}
    ~~~~~

3. Right before closing `</body>` tag add these lines:

    ~~~~~ html
    {% raw %}
    {% if page.custom_js %}
        {% for js_file in page.custom_js %}
            <script type="text/javascript" src="{{ site.baseurl }}/assets/js/{{ js_file }}.js"></script>
        {% endfor %}
    {% endif %}
    {% endraw %}
    ~~~~~

4. Save `default.html` file. Now, whenever your page has `custom_css` and (or) `custom_js` variables in your front matter, Jekyll will load custom js and css. Front matter example:

    ```
    ---
    custom_css: nameofcss
    custom_js: nameofjs
    ---
    ```

This tells Jekyll to load css file `nameofcss`.js (notice, name of the file is written without extension) and js file `nameofjs`.js for current page.

### Add music player

I developed this audioplayer for my Dad's personal website (on Jekyll as well). It plays audio files either from git repo or from provided URL link. Here how it looks on the page:

![image-20210701160324417](/assets/images/posts/2021-06-25-mm-01.png)

Styling could be better, but at least it doesn't require any external js or crazy css.

1. Copy files `/assets/js/audioplayer.js` and `/assests/css/audioplayer.css` from my repo to your assets folder (preserving the structure). 
2. Follow the instructions from previous section to tell Jekyll to load custom js and css files.
3. Create page with `single` layout and load our custom css and js for audioplayer.  [Example](https://raw.githubusercontent.com/georgy-july/georgy-july.github.io/master/index.md).
4. Add list of music files for audioplayer playlist.

    ```
    ---
    layout: single
    author_profile: true
    title: "Музыка"
    custom_css: audioplayer
    custom_js: audioplayer
    music:
      - name: "Музыка 1 | эл.гитара, флейта"
        url: /assets/audio/test.mp3
      - name: "Песня 1 | слова И. Иванова"
        url: http://www.archive.org/download/bolero_69/Bolero.mp3
      - name: "Музыка 2 | ак. гитара, романс"
        url: http://www.archive.org/download/bolero_69/Bolero.mp3
      - name: "Песня 2 | слова П. Васечкина"
        url: http://www.archive.org/download/bolero_69/Bolero.mp3
      - name: "Песня с длинным названием и описанием | эл. гитара, бас, барабаны | слова С. Миронов"
        url: http://www.archive.org/download/bolero_69/Bolero.mp3
      - name: "Музыка 3 | пианино, флейта"
        url: http://www.archive.org/download/bolero_69/Bolero.mp3
      - name: "Синий гном"
        url: /assets/audio/test.mp3
    ---
    ```

5. Include player and the playlist in the page body. As you can see player is muted by default, autoplay is turned off:

    ~~~~~ html
    {% raw %}
    Hello!
    
    Chose a track in the playlist and hit play!
    Don't forget to _unmute_ player. 
    
    <audio id="audio" preload="metadata" autoplay="false" muted tabindex="0" controls type="audio/mp3">
      <source type="audio/mp3" src="">
      Sorry, your browser does not support HTML5 audio.
    </audio>
    <ul id="playlist">
      {% for track in page.music %}
        <li>
          <a href="{{ track.url }}"><span class="fas fa-play-circle fa-sm" aria-hidden="true">  {{ track.name }}</span></a>
        </li>
      {% endfor %}
    </ul>
    {% endraw %}
    ~~~~~

6. Build the site and go to the page with audioplayer. Unmute the player, choose a song and play!

## Move to GitHub Pages

 Change `Gemfile` to the Github Pages version:

```yaml
source "https://rubygems.org"

gem "github-pages", group: :jekyll_plugins

gem "tzinfo-data"
gem "wdm", "~> 0.1.0" if Gem.win_platform?

# If you have any plugins, put them here!
group :jekyll_plugins do
  gem "jekyll-paginate"
  gem "jekyll-sitemap"
  gem "jekyll-gist"
  gem "jekyll-feed"
  gem "jemoji"
  gem "jekyll-include-cache"
  gem "jekyll-algolia"
end

```

Comment out `theme` variable in your `_config.yml` and add `remote_theme`:

```yaml
remote_theme          : mmistakes/minimal-mistakes
```

Make sure you don't have any extra plugins except for approved by GitHub Pages:

 ```yaml
 plugins:
   - jekyll-paginate
   - jekyll-sitemap
   - jekyll-gist
   - jekyll-feed
   - jemoji
   - jekyll-include-cache
 ```



