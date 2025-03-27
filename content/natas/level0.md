+++
title = "Level 0"
date = 2025-03-27T14:45:00+01:00
draft = false
+++

-   State "DONE"       from              <span class="timestamp-wrapper"><span class="timestamp">[2025-03-27 Thu 14:45]</span></span>

First we go to the link that is given and log in using the credentials above. We then get a message saying `You can find the password for the next level on this page.`

However, the password is not visible. Looking at the page source we find the following:

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
    <script>var wechallinfo = { "level": "natas0", "pass": "natas0" };</script></head>
  <body>
    <h1>natas0</h1>
    <div id="content">
      You can find the password for the next level on this page.

      <!--The password for natas1 is 0nzCigAq7t2iALyvU9xcHlYN4MlkIwlq -->
    </div>
  </body>
</html>
```

THere is our password!

-   0nzCigAq7t2iALyvU9xcHlYN4MlkIwlq
