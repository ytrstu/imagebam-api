# Get Gallery Images #

## Introduction ##

The function returns all images contained in an existing gallery.

A gallery consists of a minimum of 2 and a maximum of 500 images. The images are interlinked so that navigation to the previous or next image are easy steps for the viewer.

Each gallery is identified by a unique ID (**GID**). The **GID** is a 32 character, alphanumeric, non-case-sensitive string.


## Fetching the List of Images in a Galleries ##

Parameters:
  * oauth\_consumer\_key
  * oauth\_token
  * oauth\_signature\_method
  * oauth\_signature
  * oauth\_timestamp
  * oauth\_nonce
  * response\_format _(optional, default: JSON)_
  * gallery\_id


Currently available response formats are:
  * JSON

The above parameters should be sent via POST method to following URL:
```
http://www.imagebam.com/sys/API/resource/get_gallery_images
```

If all parameters check out you will receive the response in the requested format.

Example **JSON** Response:
```
{"rsp":{
   "status":"ok",
   "images":[
      {"ID":"123412341234","URL":"http:\/\/www.imagebam.com\/image\/XXXXXXXXXX","thumbnail":"http:\/\/thumbnails23.imagebam.com\/10855\/XXXXXXX.jpg","GID":"XXXXXXXXXXXXXXX"},
      {"ID":"123412341234","URL":"http:\/\/www.imagebam.com\/image\/XXXXXXXXXX","thumbnail":"http:\/\/thumbnails23.imagebam.com\/10855\/XXXXXXX.jpg","GID":"XXXXXXXXXXXXXXX"},
      {"ID":"123412341234","URL":"http:\/\/www.imagebam.com\/image\/XXXXXXXXXX","thumbnail":"http:\/\/thumbnails23.imagebam.com\/10855\/XXXXXXX.jpg","GID":"XXXXXXXXXXXXXXX"}
      ]
   }
}
```


The request can fail for the following reasons:
  * invalid OAuth authentication
  * invalid gallery\_id
  * missing gallery\_id

In the case of an invalid OAuth authentication the response will be in the format required by the OAuth Core 1.0a.




## Example Code ##
### PHP using cURL ###
```
<?php
// required parameters for the OAuth-authentication
// replace all parameters!
$API_key = "XXXXXXXXXXXX";
$API_secret = "XXXXXXXXXXXXXX";
$oauth_nonce = "XXXXX";
$oauth_token = "XXXXXXXXXXXXXXXXX";
$oauth_token_secret = "XXXXXXXXXXXX";
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
$pvars = array(
// parameters required by OAuth
"oauth_consumer_key" => $API_key,
"oauth_signature_method" => $oauth_signature_method,
"oauth_signature" => $oauth_signature,
"oauth_timestamp" => $oauth_timestamp,
"oauth_nonce" => $oauth_nonce,
"oauth_token" => $oauth_token,

// optional parameters
"response_format" => "JSON",
"gallery_id" => "XXXXXXXXXXXXXXXXXXX"
);

// initialize curl and set parameters
$curl = curl_init();
curl_setopt($curl, CURLOPT_URL, 'http://www.imagebam.com/sys/API/resource/get_gallery_images');
curl_setopt($curl, CURLOPT_TIMEOUT, 30);
curl_setopt($curl, CURLOPT_POST, 1);
curl_setopt($curl, CURLOPT_POSTFIELDS, $pvars);
curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);

// execute, get information and close connection
$response = curl_exec($curl);
$info = curl_getinfo($curl);
curl_close ($curl);


// check if the http-code is 200
if($info['http_code'] != 200){
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