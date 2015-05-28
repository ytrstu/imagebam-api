# Get Existing Galleries #

## Introduction ##

A gallery consists of a minimum of 2 and a maximum of 500 images. The images are interlinked so that navigation to the previous or next image are easy steps for the viewer.

Each gallery is identified by a unique ID (**GID**). The **GID** is a 32 character, alphanumeric, non-case-sensitive string.

A gallery must be created before images can be added to it.

Each gallery consist of the following information:
  * GID
  * URL
  * title (max. length 100 characters, no HTML)
  * description (max. length 500 characters, no HTML)

In order to upload an image into an existing gallery a valid **GID** has to be passed along. The following resource returns a list of all existing galleries and their respective URL, title and description.

## Fetching the List of Galleries ##

Parameters:
  * oauth\_consumer\_key
  * oauth\_token
  * oauth\_signature\_method
  * oauth\_signature
  * oauth\_timestamp
  * oauth\_nonce
  * response\_format _(optional, default: JSON)_


Currently available response formats are:
  * JSON

The above parameters should be sent via GET or POST method to following URL:
```
http://www.imagebam.com/sys/API/resource/get_galleries
```

If all parameters check out you will receive the response in the requested format.

Example **JSON** Response:
```
{"rsp":
	{"status":"ok","galleries":[
		{"GID":"12345x93id0ubn10y84pe2n3mp04cezi","URL":"http:\/\/www.imagebam.com\/gallery\/12345x93id0ubn10y84pe2n3mp04cezi","title":"This is a title!","description":"This is a description."},
		{"GID":"123456dmlo8unex8qtvvq44wbafbfevq","URL":"http:\/\/www.imagebam.com\/gallery\/123456dmlo8unex8qtvvq44wbafbfevq","title":"This is another title!","description":"This is another description."},
		{"GID":"123450fv89184wjb90kmuvw8euwcn806","URL":"http:\/\/www.imagebam.com\/gallery\/123450fv89184wjb90kmuvw8euwcn806","title":"A gallery without a description","description":""}
	]}
}
```


The request can fail for the following reasons:
  * invalid OAuth authentication

In the case of an invalid OAuth authentication the response will be in the format required by the OAuth Core 1.0a.




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
        "oauth_token" => $oauth_token
);

// initialize curl and set parameters
$curl    = curl_init();
curl_setopt($curl, CURLOPT_URL, 'http://www.imagebam.com/sys/API/resource/get_galleries');
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