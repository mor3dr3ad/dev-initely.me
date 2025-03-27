+++
title = "Level 11"
date = 2025-03-27T14:45:00+01:00
draft = false
+++

-   State "DONE"       from              <span class="timestamp-wrapper"><span class="timestamp">[2025-03-27 Thu 14:45]</span></span>

<http://natas11.natas.labs.overthewire.org/>

This level seems to work with an encryption called XOR encryption:

{{< figure src="/ox-hugo/clipboard-20250301T221818.png" >}}


## Background {#background}

Okay, some theory first: what is an XOR cipher? It is a type of additive cipher that works with the applying the bitwise XOR operator to every character using a given key. To decrypt, applying the XOR function with the key will remove the cipher. To take an example from Wikipedia, let's take the string 'Wiki' and encode it in 8-bit ASCII and encode it with the key 11110011:

{{< figure src="/ox-hugo/clipboard-20250301T224230.png" >}}

Above it says that cookies are encoded with XOR encryption. So let's use our browser to check the cookies.

{{< figure src="/ox-hugo/clipboard-20250301T224501.png" >}}

As expected, the cookies contains some ciphertext: `HmYkBwozJw4WNyAAFyB1VUcqOE1JZjUIBis7ABdmbU1GdGdfVXRnTRg=`


## Understanding the code {#understanding-the-code}

Then we look at the sourcecode:

```php
  <?
$defaultdata = array( "showpassword"=>"no", "bgcolor"=>"#ffffff");

function xor_encrypt($in) {
    $key = '<censored>';
    $text = $in;
    $outText = '';

    // Iterate through each character
    for($i=0;$i<strlen($text);$i++) {
    $outText .= $text[$i] ^ $key[$i % strlen($key)];
    }

    return $outText;
}

function loadData($def) {
    global $_COOKIE;
    $mydata = $def;
    if(array_key_exists("data", $_COOKIE)) {
    $tempdata = json_decode(xor_encrypt(base64_decode($_COOKIE["data"])), true);
    if(is_array($tempdata) && array_key_exists("showpassword", $tempdata) && array_key_exists("bgcolor", $tempdata)) {
        if (preg_match('/^#(?:[a-f\d]{6})$/i', $tempdata['bgcolor'])) {
        $mydata['showpassword'] = $tempdata['showpassword'];
        $mydata['bgcolor'] = $tempdata['bgcolor'];
        }
    }
    }
    return $mydata;
}

function saveData($d) {
    setcookie("data", base64_encode(xor_encrypt(json_encode($d))));
}

$data = loadData($defaultdata);

if(array_key_exists("bgcolor",$_REQUEST)) {
    if (preg_match('/^#(?:[a-f\d]{6})$/i', $_REQUEST['bgcolor'])) {
        $data['bgcolor'] = $_REQUEST['bgcolor'];
    }
}
saveData($data);
?>
```

```php { linenos=true, linenostart=1 }
<?
if($data["showpassword"] == "yes") {
    print "The password for natas12 is <censored><br>";
}

?>
```

This is a bit more involved, so let's look at this step by step.

```php
$defaultdata = array( "showpassword"=>"no", "bgcolor"=>"#ffffff");
```

This sets the default value for the array `$defaultdata`. Then we have a function that encrypts using the XOR cipher with a `$key`.

```php { linenos=true, linenostart=1 }
  function xor_encrypt($in) {
    $key = '<censored>';
    $text = $in;
    $outText = '';

    // Iterate through each character
    for($i=0;$i<strlen($text);$i++) {
        $outText .= $text[$i] ^ $key[$i % strlen($key)];
    }

    return $outText;
}
```

The following function then reads data from the cookie and decrypts it using `base64`, `xor_encrypt` (XOR encryption is reversible) and then parses it as `json_decode()`.

The function then checks if the loaded and decrypted array is valid and contains `"showpassword"` and `"bgcolor"`. If so, then it updates `$mydata`.

```php { linenos=true, linenostart=1 }
  function loadData($def) {
    global $_COOKIE;
    $mydata = $def;

    if(array_key_exists("data", $_COOKIE)) {
        $tempdata = json_decode(xor_encrypt(base64_decode($_COOKIE["data"])), true);

        if(is_array($tempdata) && array_key_exists("showpassword", $tempdata) && array_key_exists("bgcolor", $tempdata)) {
            if (preg_match('/^#(?:[a-f\d]{6})$/i', $tempdata['bgcolor'])) {
                $mydata['showpassword'] = $tempdata['showpassword'];
                $mydata['bgcolor'] = $tempdata['bgcolor'];
            }
        }
    }

    return $mydata;
}
```

Then there is a function that saves the data to cookies:

```php
  function saveData($d) {
    setcookie("data", base64_encode(xor_encrypt(json_encode($d))));
}
```

So let's gather what we have:

-   We have a reversible cipher (XOR cipher) and both
-   cleartext (the value stored in `$defaultdata`)
-   ciphertext (the value stored in the cookie)


## Obtaining the key {#obtaining-the-key}

So if we XOR the ciphertext with the known plaintext, we should get the key. Break this down into several steps:

-   First encode the original plaintext to json and then base64

<!--listend-->

```php
$defaultdata = array( "showpassword"=>"no", "bgcolor"=>"#ffffff");
echo base64_encode(json_encode($defaultdata));
```

-   Then take the plaintext data and encrypt it with the key obtained from the cookie.

<!--listend-->

```php { linenos=true, linenostart=1 }
  function xor_encrypt($in, $key) {
    $text = $in;
    $outText = '';

    // Iterate through each character
    for($i=0;$i<strlen($text);$i++) {
    $outText .= $text[$i] ^ $key[$i % strlen($key)];
    }

    return $outText;
}

  $plaintext = 'eyJzaG93cGFzc3dvcmQiOiJubyIsImJnY29sb3IiOiIjZmZmZmZmIn0=';
  $decoded_data = base64_decode($plaintext);
  $key = 'HmYkBwozJw4WNyAAFyB1VUcqOE1JZjUIBis7ABdmbU1GdGdfVXRnTRg=';
  $decoded_key = base64_decode($key);
  $encrypted = xor_encrypt($decoded_data, $decoded_key);
  echo $encrypted;
```

This gives us the string `eDWoeDWoeDWoeDWoeDWo` on repeat. So it seems that our key might be `eDWo`.


## Modifying the plaintext {#modifying-the-plaintext}

Now we have the supposed key for encryption, and we know that we want to set the `showpassword` bit to yes in the json array. So first change the cleartext, create a JSON text object from it and encode it to base64 (just for readability).

```php
$defaultdata = array( "showpassword"=>"yes", "bgcolor"=>"#ffffff");
echo base64_encode(json_encode($defaultdata));
```

This gives us:

```text
eyJzaG93cGFzc3dvcmQiOiJ5ZXMiLCJiZ2NvbG9yIjoiI2ZmZmZmZiJ9
```

Then we use the functions we have already utilized above to encode the new plaintext with the obtained key to get the new cookie value.

```php { linenos=true, linenostart=1 }
  function xor_encrypt($in, $key) {
    $text = $in;
    $outText = '';

    // Iterate through each character
    for($i=0;$i<strlen($text);$i++) {
    $outText .= $text[$i] ^ $key[$i % strlen($key)];
    }

    return $outText;
}

  $plaintext = 'eyJzaG93cGFzc3dvcmQiOiJ5ZXMiLCJiZ2NvbG9yIjoiI2ZmZmZmZiJ9';
  $decoded_data = base64_decode($plaintext);
  $key = 'eDWo';
  $encrypted = xor_encrypt($decoded_data, $key);
  $cookie_value = base64_encode($encrypted);
  echo $cookie_value;
```

Finally, use the value below and use the developer tab in your browser to replace the cookie value with this. This should get the website to show you the password.

```text
HmYkBwozJw4WNyAAFyB1VUc9MhxHaHUNAic4Awo2dVVHZzEJAyIxCUc5
```

-   yZdkjAYZRd3R7tq7T5kXMjMJlOIkzDeB


## Security implications {#security-implications}

There are some implications to this exercise:

-   XOR encryption is a simple bitwise operation, not a cryptographically secure algorithm. So if an attacker know any part of the plaintext and access to the encrypted data (e.g. via a cookie), the key can easily be derived.
-   Storing sensitive information in cookies is risky; cookies can be tampered with. We should instead use secure cookes with HTTP-only &amp; HTTPS or session-based authentication.
-   We should use a more secure encryption such as AES.
