Installation
============

> Note: This section is under development.

There are two ways you can install the Yii framework:

* Via [Composer](http://getcomposer.org/) (recommended)
* By downloading an application template containing all of the site requirements, including the Yii framework itself


Installing via Composer
-----------------------

The recommended way to install Yii is to use the [Composer](http://getcomposer.org/) package manager. If you do not already
have Composer installed, you may download it from [http://getcomposer.org/](http://getcomposer.org/), or run the following command to download and install it:

```
curl -s http://getcomposer.org/installer | php
```

(It is strongly recommended to perform a [global Composer installation](https://getcomposer.org/doc/00-intro.md#globally)).

For problems with, or more information on, installing Composer, see the official Composer guide:

* [Linux](http://getcomposer.org/doc/00-intro.md#installation-nix)
* [Windows](http://getcomposer.org/doc/00-intro.md#installation-windows)

With Composer installed, you can create a new Yii site using one of Yii's ready-to-use application templates. Based on your needs, choosing the right template can help bootstrap your project.

Currently, there are two Yii application templates available:

- [Basic Application Template](https://github.com/yiisoft/yii2-app-basic), a basic frontend application template
- [Advanced Application Template](https://github.com/yiisoft/yii2-app-advanced), consisting of a frontend, a backend, console resources, common (shared code), and support for environments

For template installation instructions, see the above linked pages.
To read more about the ideas behind these application templates and the proposed usage,
refer to the [basic application template](apps-basic.md) and [advanced application template](apps-advanced.md) documents.

If you do not want to use a template, rather starting from scratch, you'll find information in the [creating your own application structure](apps-own.md) document. This approach is only recommended for advanced users.


Installing from zip
-------------------

Installation from a zip file involves two steps:

   1. Downloading an application template from [yiiframework.com](http://www.yiiframework.com/download/).
   2. Unpacking the downloaded file.

If you only want the Yii Framework files you can download a zip file directly from [github](https://github.com/yiisoft/yii2-framework/releases).
To create your application you might want to follow the steps described in [creating your own application structure](apps-own.md).
This is only recommended for advanced users.

> Tip: The Yii framework itself does not need to be installed under a web-accessible directory (in fact, it should not be).
A Yii application has one entry script, which is usually the only file that absolutely must be
exposed to web users (i.e., placed within the web directory). Other PHP scripts, including those
in the Yii Framework, should be protected from web access to prevent possible exploitation by hackers.


Requirements
------------

Yii 2 requires PHP 5.4.0 or higher. Yii has been tested with the [Apache HTTP server](http://httpd.apache.org/) and
[Nginx HTTP server](http://nginx.org/) on both Windows and Linux.
Yii may also be usable on other web servers and platforms, provided that PHP 5.4 or higher is present.

After installing Yii, you may want to verify that your server satisfies
Yii's requirements. You can do so by running the requirement checker
script in a web browser or from the command line.

If you have installed a Yii application template via the downloaded zip file or Composer, you'll find a `requirements.php` file in the
base directory of your application.

In order to run this script on the command line use the following command (after navigating to the directory where `requirements.php` can be found):

```
php requirements.php
```

In order to run this script in your browser, you must make sure it's within a web directory, and then
access `http://hostname/path/to/yii-app/requirements.php` in your browser.


Recommended Apache Configuration
--------------------------------

Yii is ready to work with a default Apache web server configuration. As a security measure, Yii comes with `.htaccess`
files in the Yii framework folder to deny access to the application's restricted resources.

By default, requests for pages in a Yii-based site go through the bootstrap file, usually named `index.php`, and placed
in the application's home directory. The result will be URLs in the format `http://hostname/index.php/controller/action/param/value`.

To hide the bootstrap file in your URLs, add `mod_rewrite` instructions to the `.htaccess` file in your web document root
(or add the instructions to the virtual host configuration in Apache's `httpd.conf` file, `Directory` section for your webroot).
The applicable instructions are:

~~~
RewriteEngine on

# If a directory or a file exists, use the request directly
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
# Otherwise forward the request to index.php
RewriteRule . index.php
~~~


Recommended Nginx Configuration
-------------------------------

Yii can also be used with the popular [Nginx](http://wiki.nginx.org/) web server, so long it has PHP installed as
an [FPM SAPI](http://php.net/install.fpm). Below is a sample host configuration for a Yii-based site on Nginx.
The configuration tells the server to send all requests for non-existent resources through the bootstrap file,
resulting in "prettier" URLs without the need for `index.php` references.

```
server {
    set $yii_bootstrap "index.php";
    charset utf-8;
    client_max_body_size 128M;

    listen 80; ## listen for ipv4
    #listen [::]:80 default_server ipv6only=on; ## listen for ipv6

    server_name mysite.local;
    root        /path/to/project/web;
    index       $yii_bootstrap;

    access_log  /path/to/project/log/access.log  main;
    error_log   /path/to/project/log/error.log;

    location / {
        # Redirect everything that isn't real file to yii bootstrap file including arguments.
        try_files $uri $uri/ /$yii_bootstrap?$args;
    }

    # uncomment to avoid processing of calls to unexisting static files by yii
    #location ~ \.(js|css|png|jpg|gif|swf|ico|pdf|mov|fla|zip|rar)$ {
    #    try_files $uri =404;
    #}
    #error_page 404 /404.html;

    location ~ \.php$ {
        include fastcgi.conf;
        fastcgi_pass   127.0.0.1:9000;
        #fastcgi_pass unix:/var/run/php5-fpm.sock;
    }

    location ~ /\.(ht|svn|git) {
        deny all;
    }
}
```

When using this configuration, you should set `cgi.fix_pathinfo=0` in the `php.ini` file in order to avoid many unnecessary system `stat()` calls.


Note that when running a HTTPS server you need to add `fastcgi_param HTTPS on;` in order for Yii to properly detect if
connection is secure.
