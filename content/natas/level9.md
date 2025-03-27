+++
title = "Level 9"
date = 2025-03-27T14:45:00+01:00
draft = false
+++

-   State "DONE"       from              <span class="timestamp-wrapper"><span class="timestamp">[2025-03-27 Thu 14:45]</span></span>

<http://natas9.natas.labs.overthewire.org/>

Level 9 again presents a search box and a button to inspect the source code, see screenshot below.

{{< figure src="/ox-hugo/clipboard-20250227T145045.png" >}}

Looking at the sourcecode, we find the following php code:

```web { linenos=true, linenostart=1 }
<?
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
$key = $_REQUEST["needle"];
}

if($key != "") {
passthru("grep -i $key dictionary.txt");
}
?>
```

The code first takes the user input posted and stores it in a variable `$key`. It then checks if key is non-empty and passes the variable to the `passthru()` function. So let's do some digging. A web search for the function gives us the following:

> The passthru() function is similar to the exec() function in that it executes a command. This function should be used in place of exec() or system() when the output from the Unix command is binary data which needs to be passed directly back to the browser. A common use for this is to execute something like the pbmplus utilities that can output an image stream directly. By setting the Content-type to image/gif and then calling a pbmplus program to output a gif, you can create PHP scripts that output images directly.

So it seems that the function takes an input and passes it through to the server. However, there is no mention of any sanitization being done - maybe we can exploit this to our advantage. So let's try to tack on some commands in our search box:

For example typing `test;ls;` into our search box gives us the following:

{{< figure src="/ox-hugo/clipboard-20250227T150331.png" >}}

Wow, this worked! So it seems we can indeed **inject** code into the server. From level 7 we remember that passes are in `/etc/natas_webpass/`, so let's try and read the contents of the folder: `test;ls /etc/natas_webpass`:

{{< figure src="/ox-hugo/clipboard-20250227T150852.png" >}}

Cool, that also works. Now the final step:

`test;cat /etc/natas_webpass/natas10;`

{{< figure src="/ox-hugo/clipboard-20250227T150954.png" >}}

Et voila - there is our password.

-   t7I5VHvpa14sJTUGV0cbEsbYfFP2dmOu
