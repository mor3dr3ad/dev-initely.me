+++
title = "Level 2"
date = 2025-03-27T14:45:00+01:00
tags = ["cybersecurity", "hacking"]
draft = false
+++

-   State "DONE"       from              <span class="timestamp-wrapper"><span class="timestamp">[2025-03-27 Thu 14:45]</span></span>

When we open the page, we are greeted with the following message:

![](/ox-hugo/clipboard-20250216T133029.png)
Okay, the first thing we want to do is check the source code. There is an interesting line here:

```html
<img src="files/pixel.png">
```

Looking at the picture we only see one pixel, so nothing interesting there really. However, there is another important information here, which is the filepath `files/pixel.png`. If we look at URL + `/files`, then we see that we are actually able to access this folder and find a `users.txt` file containing the password.
![](/ox-hugo/clipboard-20250216T134444.png)

-   3gqisGdR0pjm6tpkDKdIWO2hSvchLeYH
