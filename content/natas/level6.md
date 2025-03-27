+++
title = "Level 6"
date = 2025-03-27T14:45:00+01:00
draft = false
+++

-   State "DONE"       from              <span class="timestamp-wrapper"><span class="timestamp">[2025-03-27 Thu 14:45]</span></span>

<http://natas6.natas.labs.overthewire.org/>

user: natas5

When we open the webpage we are greeted with the following landing page:

{{< figure src="/ox-hugo/clipboard-20250224T155951.png" >}}

So we are looking for some kind of secret to input. There is also an auspicious button on the bottom right that lets us view the sourcecode.

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
        <script>var wechallinfo = { "level": "natas6", "pass": "<censored>" };</script></head>
    <body>
        <h1>natas6</h1>
        <div id="content">

            <?

            include "includes/secret.inc";

            if(array_key_exists("submit", $_POST)) {
            if($secret == $_POST['secret']) {
            print "Access granted. The password for natas7 is <censored>";
            } else {
            print "Wrong secret";
            }
            }
            ?>

            <form method=post>
                Input secret: <input name=secret><br>
                <input type=submit name=submit>
            </form>

            <div id="viewsource"><a href="index-source.html">View sourcecode</a></div>
        </div>
    </body>
</html>
```

This seems to be some kind of mixture of `html`, `php` and `javascript` with the code in the middle checking for some secret and if that secret is matched, the code will presumably return the password. Only problem: we do not know the secret unfortunately.

However, there is one hint in the source code in the line `include "includes/secret.inc"` we seem to have a filepath. And if we add this path to the webpage, we indeed get a result:

```web
<?
$secret = "FOEIUWGHFEEUHOFUOIU";
?>
```

And indeed: if we insert this secret into our field, we get the secret back!

-   bmg8SvU1LizuWjx3y7xkNERkHxGre0GS
