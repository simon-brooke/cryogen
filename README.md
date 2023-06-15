<img src="https://raw.githubusercontent.com/lacarmen/cryogen/master/cryogen.png"
 hspace="20" align="left" height="200"/>

<!-- Non-breaking space -->
&nbsp;

This README only documents a subset of Cryogen's features. For additional documentation please see the [cryogen site](http://cryogenweb.org).

<!-- Non-breaking space -->
&nbsp;


## Features

* Blog posts and pages with Markdown (default) or AsciiDoc
* Tags
* Table of contents generation
* Plain HTML page templates
* Code syntax highlighting
* Disqus support
* Sitemap generation
* RSS feed generation
* Sass/SCSS compilation
* Klipse Integration

## Usage

### Creating a New Site

You can create a new website using either one of `leiningen` or `clj-new` or `deps-new`.

#### Creating a New Site With Leiningen

You will need [Leiningen][1] 2.5.0 or above installed.

[1]: https://github.com/technomancy/leiningen

A new site can be created using the Cryogen leiningen template as follows:

```sh
lein new cryogen my-blog
```

#### Creating a New Site With clj-new as a Tool

Alternatively, use [`clj-new as a tool`](https://github.com/seancorfield/clj-new#installation-as-a-tool):

```sh
clojure -Ttools install com.github.seancorfield/clj-new '{:git/tag "v1.2.362"}' :as clj-new # update to latest!
clojure -Tclj-new create :template cryogen :name myname/myblog :force true
cd myname/myblog/
```

#### Creating a New Site With deps-new as a Tool

Alternatively, use [`deps-new as a tool`](https://github.com/seancorfield/deps-new#deps-new-):

```sh
clojure -Ttools install io.github.seancorfield/deps-new '{:git/tag "v0.4.0"}' :as new
clojure -Sdeps '{:deps {io.github.cryogen-project/cryogen {:git/tag "0.6.6" :git/sha "fcb2833"}}}' -Tnew create :template org.cryogenweb/new :name myname/myblog
cd myname/myblog/
```

The artifact `cryogen/lein-template` contains both a leiningen
template and a deps-new template.

### Running the Server

The web server can be started from the `my-blog` directory using either Leiningen:

```sh
lein serve # or lein serve:fast
```

or tools-deps:

```sh
clojure -X:serve # or clojure -X:serve:fast
```

The server will watch for changes in the `content` and `themes` folders and recompile the content automatically. The `*:fast` variants perform [fast but partial compilation](https://cryogenweb.org/docs/fast-compilation.html) of only the changed page/post.

You can also generate the content without bringing up a server either via:

```sh
lein run
```

or via:

```sh
clojure -M:build
```

### Site Configuration

The site configuration file is found at `content/config.edn`, this file looks as follows:

```clojure
{:site-title                   "My Awesome Blog"
 :author                       "Bob Bobbert"
 :description                  "This blog is awesome"
 :site-url                     "http://blogawesome.com/"
 :post-root                    "posts"
 :page-root                    "pages"
 :post-root-uri                "posts-output"
 :page-root-uri                "pages-output"
 :tag-root-uri                 "tags-output"
 :author-root-uri              "authors-output"
 :public-dest                  "public"
 :blog-prefix                  "/blog"
 :rss-name                     "feed.xml"
 :rss-filters                  ["cryogen"]
 :recent-posts                 3
 :post-date-format             "yyyy-MM-dd"
 :archive-group-format         "yyyy MMMM"
 :sass-src                     []
 :sass-path                    "sass"
 :theme                        "blue"
 :resources                    ["img"]
 :keep-files                   [".git"]
 :disqus?                      false
 :disqus-shortname             ""
 :ignored-files                [#"\.#.*" #".*\.swp$"]
 :previews?                    false
 :posts-per-page               5
 :blocks-per-preview           2
 :clean-urls                   :trailing-slash
 :collapse-subdirs?            false
 :hide-future-posts?           true
 :klipse                       {}
 :description-include-elements #{:p :h1 :h2 :h3 :h4 :h5 :h6}
 :debug?                       false
 :twitter-acct                 nil 
                               ;; change this to "your-twitter-handle" to 
                               ;; enable auto twitter banners
 :mastodon                     {:host nil ;; change this to the domain name 
                                          ;; of your mastodon host to generate 
                                          ;; a me-link
                                :acct nil ;; change this to your mastodon 
                                          ;; account name to generate a me-link
                                }}
```

For information about each key please see the ["Configuration"](http://cryogenweb.org/docs/configuration.html) portion of the Cryogen documentation site.

### Switching between Markdown and AsciiDoc

Cryogen comes with Markdown support as default. If you want to use AsciiDoc instead, open the `project.clj` in your created blog (e.g. `my-blog`), and change the line in `:dependencies` that says `cryogen-flexmark` to `cryogen-asciidoc` (and ensure the right version).
Instead of looking for files ending in `.md` in the `content/md` directory, the compiler will now look for files ending in `.asc` in the `content/asc` directory.

### Selecting a Theme

The Cryogen template comes with three themes in the `themes` folder. To change your blog's theme, change the value of the `:theme` key in `config.edn`. Note that the Nucleus theme is obtained from [downloadwebsitetemplates.co.uk](http://www.downloadwebsitetemplates.co.uk/template/nucleus/) that requires you to keep the footer, unless you make a donation on their website.

### Customizing Layouts

Cryogen uses [Selmer](https://github.com/yogthos/Selmer) templating engine for layouts. Please refer to its documentation to see the supported tags and filters for the layouts.

The layouts are contained in the `themes/{theme}/html` folder of the project. By default, the `base.html` layout is used to provide the general layout for the site. This is where you would add static resources such as CSS and JavaScript assets as well as define headers and footers for your site.

Each page layout should have a name that matches the `:layout` key in the page metadata and end with `.html`. Page layouts extend the base layout and should only contain the content relaveant to the page inside the `content` block.
For example, the `tag` layout is located in `tag.html` and looks as follows:

```xml
{% extends "/html/base.html" %}
{% block content %}
<div id="posts-by-tag">
    <h2>Posts tagged {{name}}</h2>
    <ul>
    {% for post in posts %}
        <li>
            <a href="{{post.uri}}">{{post.title}}</a>
        </li>
    {% endfor %}
    </ul>
</div>
{% endblock %}
```

### Code Syntax Highlighting

Cryogen uses [Highlight.js](https://highlightjs.org/) for code syntax highlighting. You can add more languages by replacing `themes/{theme}/js/highlight.pack.js` with a customized package from [here](https://highlightjs.org/download/).

The ` initHighlightingOnLoad` function is called in `themes/{theme}/html/base.html`.

```xml
<script>hljs.initHighlightingOnLoad();</script>
```

## Deploying Your Site

The generated static content will be found under the `public` folder. Simply copy the content to a static
folder for a server such as Nginx or Apache and your site is now ready for service.

A sample Nginx configuration that's placed in `/etc/nginx/sites-available/default` can be seen below:

```javascript
server {
  listen 80 default_server;
  listen [::]:80 default_server ipv6only=on;
  server_name localhost <yoursite.com> <www.yoursite.com>;

  access_log  /var/log/blog_access.log;
  error_log   /var/log/blog_error.log;

  location / {
    alias       /var/blog/;
    error_page  404 = /404.html;
  }
}
```

Simply set `yoursite.com` to the domain of your site in the above configuration and
ensure the static content is available at `/var/blog/`. Finally, place your custom error page
in the `/var/blog/404.html` file.

More information on deployment can be found [here](http://cryogenweb.org/docs/deploying-to-github-pages.html).

## Third Party Libraries

#### https://github.com/greywolve/cryogen-markdown-external

A Clojure library to provide Markdown rendering to the cryogen-core compiler by using an external command/program, such as pandoc.


## Some Sites Made With Cryogen

* [My personal blog](http://carmen.la/blog/archives)
* [Cryogen Documentation Site](http://cryogenweb.org)
* [Yogthos' blog](http://yogthos.net/)
* [Chad Stovern's blog](http://www.chadstovern.com)
* [szcz](http://www.szcz.org/)
* [ISvit blog](https://blog.isvit.info)
* [Lambda Funk](http://lambdafunk.com)
* [DDFT.wiki](https://ddft.wiki)
* [Ampersanda's blog](https://ampersanda.dev)
* [irrelevancy universe](https://irrelevancy.neocities.org)

## License

Copyright © 2014-2021 Carmen La

Distributed under the Eclipse Public License, the same as Clojure.
