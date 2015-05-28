# Authenticating your Application #

## Introduction ##

It takes a couple steps to authenticate your application.

Please read following guide provided by OAuth before continuing: http://oauth.net/core/1.0a/


## API Registration ##
First of all you will need to register your application with ImageBam.

http://www.imagebam.com/sys/API/clients

Once you have registered your application you will get an email containing:
  * your API-key
  * your API-secret
  * additional information



## General Information ##
All parameters can either be sent via **GET** or **POST**. Please note that most parameters are case-sensitive.



## Signing Requests ##
Currently only **MD5** is accepted as a signing method.
```
oauth_signature_method=MD5
```
The signature is calculated out of following parameters:
  * API-key
  * API-secret
  * oauth\_timestamp
  * oauth\_nonce
  * oauth\_token (not required when requesting a token)

For the **request\_token** method the parameters **oauth\_token** and **oauth\_token\_secret** are not required.
The **oauth\_signature** is built by concatenating the parameters above in the given order and then calculating the MD5-checksum of the string:
```
oauth_signature = MD5(API-key + API-secret + oauth_timestamp + oauth_nonce)
```

For all other requests the **oauth\_token** is also used to build the **oauth\_signature**:
```
oauth_signature = MD5(API-key + API-secret + oauth_timestamp + oauth_nonce + oauth_token + oauth_token_secret)
```


## Obtaining an Unauthorized Token ##

How to request an unauthorized token is specified in the section 6.1 of the OAuth Core 1.0a.
http://oauth.net/core/1.0a/#auth_step1

The following parameters are required:
  * oauth\_consumer\_key
  * oauth\_signature\_method
  * oauth\_signature
  * oauth\_timestamp
  * oauth\_nonce

The parameter **oauth\_callback** is currently not supported.

The parameters must be sent via POST or GET method to following URL:
```
http://www.imagebam.com/sys/oauth/request_token
```

Example POST/GET values:
```
oauth_consumer_key      D6586E6JXQUGF39O
oauth_nonce	        AADDFFGG
oauth_signature	        92dc475e2cf94dea411ee30329d55c0b
oauth_signature_method	MD5
oauth_timestamp	        1271888656
```

If all parameters check out the answer includes the following parameters:
  * oauth\_token
  * oauth\_token\_secret

Example result:
```
oauth_token             fYZ8IKDtWznm5BatSTZKz4CrWakPodgf
oauth_token_secret      WJi1lruekuYTxAkg
```

If the parameters are not correct you will receive sufficient information to address the error.



## Consumer Directs the User to the Service Provider ##

In order to exchange the _unauthorized token_ for an _access token_ the user must give his/her consent. This is outlined in the section 6.2.1 of the OAuth Core 1.0a.

The user should be sent to following URL:
```
http://www.imagebam.com/sys/oauth/authorize_token
```

The following parameter must be passed along:
  * oauth\_token

The user will be given certain information about your application and can choose to either accept or decline to give your application the required rights.

If the user chooses to accept the request a 6 character long string (**verifier**) will be printed on the page.

Your application should prompt the user to enter this **verifier**.



## Consumer Requests an Access Token ##
In order to be granted access to the protected resources the _token_ must be exchanged for an _access token_.

This is outlined in the section 6.3.1 of the OAuth Core 1.0a.

Following parameters are required:
  * oauth\_consumer\_key
  * oauth\_token
  * oauth\_signature\_method
  * oauth\_signature
  * oauth\_timestamp
  * oauth\_nonce
  * oauth\_verifier

The above parameters must be sent vie GET or POST method to following URL:
```
http://www.imagebam.com/sys/oauth/request_access_token
```

If all parameters check out the answer includes following parameters:
  * oauth\_token
  * oauth\_token\_secret

Note that the **oauth\_token\_secret** has been replaced with a new 16 character string.

Example result:
```
oauth_token             fYZ8IKDtWznm5BatSTZKz4CrWakPodgf
oauth_token_secret      RH58X8vRXF7GXKe9
```

If the parameters are not correct you will receive sufficient information to address the error.

## Accessing Protected Resources ##

You now have access to the user's 'protected resources' and are able to do following:
  * upload images
  * generate a new gallery ID
  * request a list of available galleries

If needed following functionalities might be included in the future:
  * delete images
  * delete galleries
  * change title and description of galleries
  * get a list of images stored in the user's account

With each request the following parameters must be passed along:
  * oauth\_consumer\_key
  * oauth\_token
  * oauth\_signature\_method
  * oauth\_signature
  * oauth\_timestamp
  * oauth\_nonce

Depending on the resource additional parameters might be required.

## Code Examples ##

### PHP using cURL ###
Thanks to Daniel V. for providing this example.

```
<?php

function oauth_request($url, $pvars)
{
    // initialize curl and set parameters
    $curl = curl_init();
    curl_setopt($curl, CURLOPT_URL, $url);
    curl_setopt($curl, CURLOPT_TIMEOUT, 30);
    curl_setopt($curl, CURLOPT_POST, 1);
    curl_setopt($curl, CURLOPT_POSTFIELDS, $pvars);
    curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);

    // execute, get information and close connection
    $response = curl_exec($curl);
    $info = curl_getinfo($curl);
    curl_close($curl);
    
    // Check if all went ok
    if($info['http_code'] != 200)
        return false;
    else
        return $response;
}

// API key and secret values
$API_key = "XXXXXXXXXXXXXXXX";
$API_secret = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX";

// Request unauthorized token
$oauth_nonce = "AADDFFGG";
$oauth_timestamp = time();
$oauth_signature_method = "MD5";

// oauth_signature = MD5(API-key + API-secret + oauth_timestamp + oauth_nonce)
$oauth_signature_string = "";
$oauth_signature_string .= $API_key;
$oauth_signature_string .= $API_secret;
$oauth_signature_string .= $oauth_timestamp;
$oauth_signature_string .= $oauth_nonce;
// calculate the MD5-checksum of the signature string
$oauth_signature = md5($oauth_signature_string);

// populate an array with parameters
$pvars   = array(
        // parameters required by OAuth
        "oauth_consumer_key" => $API_key,
        "oauth_signature_method" => $oauth_signature_method,
        "oauth_signature" => $oauth_signature,
        "oauth_timestamp" => $oauth_timestamp,
        "oauth_nonce" => $oauth_nonce
);

$response = oauth_request('http://www.imagebam.com/sys/oauth/request_token', $pvars);
if($response == false)
    die('OAuth error: '.$response);
    
$var_values = explode('&', $response);
foreach($var_values as $var_value)
{
    $var = explode('=', $var_value);
    $$var[0] = $var[1];
}

echo "oauth_token: ".$oauth_token.'<br />oauth_token_secret: '.$oauth_token_secret;

?>
```