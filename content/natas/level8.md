+++
title = "Level 8"
date = 2025-03-27T14:45:00+01:00
draft = false
+++

-   State "DONE"       from              <span class="timestamp-wrapper"><span class="timestamp">[2025-03-27 Thu 14:45]</span></span>

<http://natas8.natas.labs.overthewire.org/>

Level 8 is a little tricky. We are asked to input a secret again and have a button showing the sourcecode.

{{< figure src="/ox-hugo/clipboard-20250224T233738.png" >}}

So let's look at the sourcecode:

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
        <script>var wechallinfo = { "level": "natas8", "pass": "<censored>" };</script></head>
    <body>
        <h1>natas8</h1>
        <div id="content">

            <?

            $encodedSecret = "3d3d516343746d4d6d6c315669563362";

            function encodeSecret($secret) {
            return bin2hex(strrev(base64_encode($secret)));
            }

            if(array_key_exists("submit", $_POST)) {
            if(encodeSecret($_POST['secret']) == $encodedSecret) {
            print "Access granted. The password for natas9 is <censored>";
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

Looking at the sourcecode, we see that there is an encoded secret stored in a variable `$encodedSecret` using php. Usually it would be difficult to discern the cleartext secret, but if we look at the code we can actually see how the secret is encoded. So let's look at the steps taken in the function `encodeSecret()`:

1.  bin2hex: searching for bin2hex, we can find this [site](https://www.php.net/manual/de/function.bin2hex.php) which describes what the function does: `Returns an ASCII string containing the hexadecimal representation of string. The conversion is done byte-wise with the high-nibble first.` We also see that there is a reverse function `hex2bin()`. Using the code editor on its [site](https://www.php.net/manual/en/function.hex2bin.php) we can reverse the encoding.
2.  Next in line is the `strrev` function which just reverses a string. So re-reversing the string we can move to the next step.
3.  Finally, `base64_encode` encodes a given string with base64. The corresponding [site](https://www.php.net/manual/en/function.base64-decode.php) for `base64_decode` will let us use its code editor to input the reverted string and get the final secret. If we input this into the text field on the original page, we get back the password for the next level.
4.  ZE1ck82lmdGIoErlhQgWND6j2Wzz6b6t
