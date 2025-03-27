+++
title = "Level 4"
date = 2025-03-27T14:45:00+01:00
draft = false
+++

-   State "DONE"       from              <span class="timestamp-wrapper"><span class="timestamp">[2025-03-27 Thu 14:45]</span></span>

<http://natas4.natas.labs.overthewire.org/>
user: natas4

This time the webpage looks different:
![](/ox-hugo/clipboard-20250216T135754.png)
We get a message saying that we are redirected from the wrong page. This means we need to change the referer header when visiting, we can do this using curl:

```bash
curl -u natas4:QryZXc2e0zahULdHrtHxzyYkj59kUxLQ --referer http://natas5.natas.labs.overthewire.org/ http://natas4.natas.labs.overthewire.org/
```

-   0n35PkggAPm2zbEpOU802c0x0Msn1ToK
