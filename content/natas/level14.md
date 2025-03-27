+++
title = "Level 14"
date = 2025-03-27T14:45:00+01:00
draft = false
+++

-   State "DONE"       from              <span class="timestamp-wrapper"><span class="timestamp">[2025-03-27 Thu 14:45]</span></span>

<http://natas14.natas.labs.overthewire.org/>

Level 14 starts with a common username and passwword login form:

{{< figure src="/ox-hugo/clipboard-20250317T101510.png" >}}

Trying a random value yields a simple message: "Access denied". So have a look at the sourcecode:

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
<script>var wechallinfo = { "level": "natas14", "pass": "<censored>" };</script></head>
<body>
<h1>natas14</h1>
<div id="content">
    <?php
    if(array_key_exists("username", $_REQUEST)) {
    $link = mysqli_connect('localhost', 'natas14', '<censored>');
    mysqli_select_db($link, 'natas14');

    $query = "SELECT * from users where username=\"".$_REQUEST["username"]."\" and password=\"".$_REQUEST["password"]."\"";
    if(array_key_exists("debug", $_GET)) {
        echo "Executing query: $query<br>";
    }

    if(mysqli_num_rows(mysqli_query($link, $query)) > 0) {
            echo "Successful login! The password for natas15 is <censored><br>";
    } else {
            echo "Access denied!<br>";
    }
    mysqli_close($link);
} else {
?>

<form action="index.php" method="POST">
Username: <input name="username"><br>
Password: <input name="password"><br>
<input type="submit" value="Login" />
</form>
<?php } ?>
<div id="viewsource"><a href="index-source.html">View sourcecode</a></div>
</div>
</body>
</html>
```

The code first checks if a username is provided, then establishes a connection to MySQL using the username `natas14` and selects the natas14 database. Then it constructs the SQL query with the input from the input form. The script then runs the SQL query and checks if any rows match the username and password. Finally, it closes the connection.

So we see that there is some sql code in there, which lets us suspect we might deal with a SQL injection vulnerability here.


## Background: SQL injection {#background-sql-injection}

Odds are you have already heard about a SQL injection attack. They are well-known attacks due to faulty web-apps and login forms. SQL injection is a code injection attack where we manipulate SQL queries by passing parts of SQL commands via a POST form into a database/SQL query.

So let's look at the example here: the query in question is:

```sql
SELECT * FROM users WHERE username = '$username' AND password = '$password';
```

where username and password are passed in via the input fields in our form. These inputs are not 'sanitized', i.e. they are just taken as is. So we can basically pass in anything we like even fragments of SQL code. So let's assume we input `' OR '1'='1'` as password. That would give us:

```sql
SELECT * FROM users WHERE username = '' AND password = '' OR '1'='1';
```

However `'1'='1'` is always true, so the command `SELECT * FROM users` runs even though we have given no valid password for the user logged in.


## Detour: Debuggging with burpsuite {#detour-debuggging-with-burpsuite}

In the sourcecode above, we see that - if the debug parameter is set - we get the query back that is executed by the SQL engine on the server. This is potentially super helpful to debug our attack! But how do we set the parameter and see the response? This is potentially possible using the "Inspect (Firefox/Chrome)", but this is quite cumbersome, especially when you want to repeat a request several times to try different stuff. In comes burpsuite. For a quick intro, see here: [Burpsuite]({{< relref "burpsuite" >}})

Using burpsuite here it will be a lot easier for us to repeatedly insert different SQL commands and evaluate the outcome. First, go to Proxy and open the burpsuite browser. Notice the toggle button "Intercept on", this should be toggled if you want to intercept requests.

{{< figure src="/ox-hugo/clipboard-20250319T111428.png" >}}

For now, toggle the intercept off and navigate to natas level 14. Enter your credentials and login. When you see the login form, enable the intercept of burpsuite. Now let's use the SQL code snippet from above in the username field:

{{< figure src="/ox-hugo/clipboard-20250319T113040.png" >}}

When you click login, nothing will happen, as the request is intercepted. Go to burpsuite and you see the request including your input into the username field:

{{< figure src="/ox-hugo/clipboard-20250319T114128.png" >}}

Select the request and send it to the repeater (CTRL+R). There you will be able to change the parameters of the request and - repeatedly, hence the name - send it to the server again and see the response. Here you see the post request made and are able to manipulate it. So let's add the "debug" parameter to the request.

{{< figure src="/ox-hugo/clipboard-20250319T114726.png" >}}

In the center column you can see the response from the server including the SQL query we were looking for. First we see that there is one additional quotation mark, let's get rid of it. What remains is:

```sql
SELECT * from users where username="" OR "1"="1" and password=""
```

So why does this not work? This is because of the way that SQL processes operators. In SQL, `AND` takes precedence over `OR`. So the expression becomes:

```sql
SELECT * from users where username="" OR ("1"="1" and password="")
```

So unless there is a user with an empty password, `password=""` evaluates to FALSE, which gives us:

```sql
SELECT * from users where username="" OR (TRUE and FALSE)
```

`TRUE and FALSE` evaluates to `FALSE`, so unless we have an empty username (unlikely), the entire expression after `where` will evaluate to `FALSE` meaning we will see get zero output and thus we will see the "Access denied" dialogue.

In order for the expression to work, there are two ways: either we cut the SQL expression off after our injected, which can be done by using `--` or we put the injected expression at the end, i.e. in the password field. The two requests are below for reference:

```sql
SELECT * from users where username="" OR "1"="1" -- "" and password=""
SELECT * from users where username="" and password="" OR "1"="1"
```

If there is no empty username or password in the database, both `username=""` as well as `username="" and password=""` will evaluate to `FALSE` meaning the expression will be `FALSE OR TRUE` which evaluates to `TRUE` and gives us all rows of the database.


## Breaking the code {#breaking-the-code}

The SQL code here is fairly easy, so let's just go ahead and try the example above:

{{< figure src="/ox-hugo/clipboard-20250317T103710.png" >}}

We need to sanitize a bit to take account for the additional quotes in our SQL code, but then: success!

-   SdqIqBsFcz3yotlNYErZSZwblkm0lrvx


## Security implications {#security-implications}

-   Allowing unsanitized SQL input is dangerous and makes your web server vulnerable to SQL injection.
