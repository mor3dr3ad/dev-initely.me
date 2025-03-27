+++
title = "Level 1"
date = 2025-03-27T14:45:00+01:00
draft = false
+++

-   State "DONE"       from              <span class="timestamp-wrapper"><span class="timestamp">[2025-03-27 Thu 14:45]</span></span>

You can find the password for the next level on this page, but rightclicking has been blocked!

And indeed: we try to rightclick and get an annoying error message. However, a quick google search shows us that there is a shortcut for Chrome: `CTRL + U` gets us the page source

```html { linenos=true, linenostart=1 }
<html>
  <head>
    <!-- This stuff in the header has nothing to do with the level -->
    <link rel="stylesheet" type="text/css" href="http://natas.labs.overthewire.org/css/level.css">
    <link rel="stylesheet" href="http://natas.labs.overthewire.org/css/jquery-ui.css" />
    <link rel="stylesheet" href="http://natas.labs.overthewire.org/css/wechall.css" />
    <script src="http://natas.labs.overthewire.org/js/jquery-1.9.1.js"></script>
    <script src="http://natas.labs.overthewire.org/js/jquery-ui.js"></script>
    <script src=http://natas.labs.overthewire.org/js/wechall-data.js></script><script src="http://natas.labs.overthewire.org/js/wechall.js"></script>
    <script>var wechallinfo = { "level": "natas1", "pass": "0nzCigAq7t2iALyvU9xcHlYN4MlkIwlq" };</script></head>
  <body oncontextmenu="javascript:alert('right clicking has been blocked!');return false;">
    <h1>natas1</h1>
    <div id="content">
      You can find the password for the
      next level on this page, but rightclicking has been blocked!

      <!--The password for natas2 is TguMNxKo1DSa1tujBLuZJnDUlCcUAPlI -->
    </div>
  </body>
</html>
```

-   TguMNxKo1DSa1tujBLuZJnDUlCcUAPlI
