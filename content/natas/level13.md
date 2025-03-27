+++
title = "Level 13"
date = 2025-03-27T14:45:00+01:00
tags = ["cybersecurity", "hacking"]
draft = false
+++

-   State "DONE"       from              <span class="timestamp-wrapper"><span class="timestamp">[2025-03-27 Thu 14:45]</span></span>

<http://natas13.natas.labs.overthewire.org/>

{{< figure src="/ox-hugo/clipboard-20250316T214024.png" >}}

This one looks very similar to the prior exercise, so let's start the same way and look at the source code.

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
<script>var wechallinfo = { "level": "natas13", "pass": "<censored>" };</script></head>
<body>
<h1>natas13</h1>
<div id="content">
For security reasons, we now only accept image files!<br/><br/>

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

    $err=$_FILES['uploadedfile']['error'];
    if($err){
        if($err === 2){
            echo "The uploaded file exceeds MAX_FILE_SIZE";
        } else{
            echo "Something went wrong :/";
        }
    } else if(filesize($_FILES['uploadedfile']['tmp_name']) > 1000) {
        echo "File is too big";
    } else if (! exif_imagetype($_FILES['uploadedfile']['tmp_name'])) {
        echo "File is not an image";
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

The code introduces some additional file checking, specifically here:

```php
} else if (! exif_imagetype($_FILES['uploadedfile']['tmp_name'])) {
      echo "File is not an image";
```

So let's look up the `exif_imagetype` function:

> exif_imagetype() reads the first bytes of an image and checks its signature.

So it seems that `exif_imagetype()` checks the [magic number](https://en.wikipedia.org/wiki/Magic_number_(programming)) of the file we want to upload. Could it be as easy as adding the magic number to our webshell? Let's try that

```shell
sed -i '1s/^/\xFF\xD8\xFF\xE0\n/' webshell.php
```

Yeah, that worked!

{{< figure src="/ox-hugo/clipboard-20250316T215853.png" >}}

The rest follows the same logic as before: use the `?cmd=` parameter to access the webshell, ignore the additional characters:

-   z3UYcr4v4uBpeX8f7EZbMHlzK4UR2XtQ


## Security implications {#security-implications}

-   exif_imagetype is clearly not enough to check for filetypes, you should disable php execution in the upload directory or use stricter server-side MIME checks.
