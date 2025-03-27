+++
title = "Level 10"
date = 2025-03-27T14:45:00+01:00
draft = false
+++

-   State "DONE"       from              <span class="timestamp-wrapper"><span class="timestamp">[2025-03-27 Thu 14:45]</span></span>

<http://natas10.natas.labs.overthewire.org/>

Level 10 starts off the same way as level 9, however it seems that now there is a filter against certain characters.
![](/ox-hugo/clipboard-20250227T233639.png)

Looking at the sourcecode, we see that the characters `;|&` are now disallowed. However, space is not disallowed.

```web { linenos=true, linenostart=1 }
<?
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
$key = $_REQUEST["needle"];
}

if($key != "") {
if(preg_match('/[;|&]/',$key)) {
print "Input contains an illegal character!";
} else {
passthru("grep -i $key dictionary.txt");
}
}
?>
```

So the strategy from level 9 will not work anymore. However, there is another way to exploit this - we can use the fact that `grep` can search through several files.

So let's look at the expression `grep -i $key dictionary.txt`. If we insert a regex into grep `.*` for matching all characters and also gives it the file `/etc/natas_webpass/natas11`. As space is not disallowed, we can put this together which would give us this `grep -i .* /etc/natas_webpass/natas11 dictionary.txt`. This expression would give us all content of the two files given.

So let's try this:

{{< figure src="/ox-hugo/clipboard-20250227T235043.png" >}}

Very good, that worked!

-   UJdqkK1pTu6VLt9UHWAgRZz6sVUZ3lEk
