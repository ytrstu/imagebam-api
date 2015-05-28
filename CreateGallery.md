# Create a Gallery #

## Introduction ##

A gallery consists of a minimum of 2 and a maximum of 500 images. The images are interlinked so that navigation to the previous or next image are easy steps for the viewer.

Each gallery is identified by a unique ID (**GID**). The **GID** is a 32 character, alphanumeric, non-case-sensitive string.

A gallery must be created before images can be added to it.

Each gallery has the following parameters:
  * GID
  * title (max. length 100 characters, no HTML)
  * description (max. length 500 characters, no HTML)


Certain special characters are automatically removed from the **title** and **description**.

## Generating a new Gallery ##

Parameters:
  * oauth\_consumer\_key
  * oauth\_token
  * oauth\_signature\_method
  * oauth\_signature
  * oauth\_timestamp
  * oauth\_nonce
  * response\_format _(optional, default: JSON)_
  * title _(optional)_
  * description _(optional)_


Currently available response formats are:
  * JSON

The above parameters should be sent via GET or POST method to following URL:
```
http://www.imagebam.com/sys/API/resource/create_gallery
```

If all parameters check out you will receive the response in the requested format.

Example **JSON** Response:
```
{
     "rsp": {
          "stat": "ok", 
           "gallery": {
                    "GID":"asdfqwertzuiqwerasdfqwertzuiqwer",
                    "URL":"http://www.imagebam.com/gallery/asdfqwertzuiqwerasdfqwertzuiqwer",
                    "title":"This is just an example title.",
                    "description":"This is just an example description."
           }
     }
}
```


The request can fail for the following reasons:
  * invalid OAuth authentication
  * other invalid parameters

In the case of an invalid OAuth authentication the response will be in the format required by the OAuth Core 1.0a.

A documentation of the possible error-codes and error-messages is still a work in progress.


## Example Code ##
### PHP using cURL ###
```
<?php
// required parameters for the OAuth-authentication
// replace all parameters!
$API_key = "D6586E6JXQUGF39O";
$API_secret = "D89QLUJPLQU5KH3WM75SLBBI16YG875L";
$oauth_nonce = "123456";
$oauth_token = "8jnRVVaKj1SmadeAmlU9IYBUOgVT68Gd";
$oauth_token_secret = "fXCoaPYwaTESRfNF";
$oauth_timestamp = time();
$oauth_signature_method = "MD5";

// build the signature string
$oauth_signature_string = "";
$oauth_signature_string .= $API_key;
$oauth_signature_string .= $API_secret;
$oauth_signature_string .= $oauth_timestamp;
$oauth_signature_string .= $oauth_nonce;
$oauth_signature_string .= $oauth_token;
$oauth_signature_string .= $oauth_token_secret;


// calculate the MD5-checksum of the signature string
$oauth_signature = md5($oauth_signature_string);

// populate an array with parameters
$pvars   = array(
        // parameters required by OAuth
        "oauth_consumer_key" => $API_key,
        "oauth_signature_method" => $oauth_signature_method,
        "oauth_signature" => $oauth_signature,
        "oauth_timestamp" => $oauth_timestamp,
        "oauth_nonce" => $oauth_nonce,
        "oauth_token" => $oauth_token,
        
        // optional parameters
        "response_format" => "JSON",
        "title" => "This is an example.",
        "description" =>"This is just an example description."
);

// initialize curl and set parameters
$curl    = curl_init();
curl_setopt($curl, CURLOPT_URL, 'http://www.imagebam.com/sys/API/resource/create_gallery');
curl_setopt($curl, CURLOPT_TIMEOUT, 30);
curl_setopt($curl, CURLOPT_POST, 1);
curl_setopt($curl, CURLOPT_POSTFIELDS, $pvars);
curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);

// execute, get information and close connection
$response = curl_exec($curl);
$info = curl_getinfo($curl);
curl_close ($curl);


// check if the http-code is 200
if($info['http_code'] != 200) {
    exit("OAuth error: ".$response);
}

// decode the json-encoded response
$response_array = json_decode($response);

// print nicely
print("<pre>");
print_r($response_array);
print("</pre>");
?>
```