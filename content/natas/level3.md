+++
title = "Level 3"
date = 2025-03-27T14:45:00+01:00
draft = false
+++

-   State "DONE"       from              <span class="timestamp-wrapper"><span class="timestamp">[2025-03-27 Thu 14:45]</span></span>

<http://natas3.natas.labs.overthewire.org/>
user: natas3

This level starts off the same as before with a screen showing "There is nothing on this page". We check the source code for the webpage again and see that this time there is no leak, but an important hint:

![](/ox-hugo/clipboard-20250216T135120.png)
This is the important line: `Not even Google will find it this time...`, which is a hint at an existing `robots.txt`. Indeed, if we type in the URL + `/robots.txt`, we find a disallowed directory `/s3cr3t/` containing a `user.txt` and the password for level 4.

-   QryZXc2e0zahULdHrtHxzyYkj59kUxLQ
