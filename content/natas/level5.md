+++
title = "Level 5"
date = 2025-03-27T14:45:00+01:00
draft = false
+++

-   State "DONE"       from              <span class="timestamp-wrapper"><span class="timestamp">[2025-03-27 Thu 14:45]</span></span>

<http://natas5.natas.labs.overthewire.org/>

user: natas5

The next level is a bit more difficult. We see a message that the user is not logged in. Looking at the page source code does not yield anything usable.

Now we need to think about how we log into a page: usually we go to a login page, however appending `/login` does not yield any result. Let us think about how a login session is usually stored in the browser: usually this is done via the use of cookies.

So let's check the Cookies via the browser's development tools. Where to find them depends on the browser used, e.g. for Chrome we use `Right Click -> Inspect` and then go to the 'Application' Tab and look at the Cookies.

If you look at the screenshot you will see that there is a cookie named `loggedin` whose value is 0. We can change the value to 1 and reload the site, which afterwards shows the password.

{{< figure src="/ox-hugo/clipboard-20250216T210541.png" >}}

-   0RoJwHdSKWFTYR5WuiAewauSuNaBXned
