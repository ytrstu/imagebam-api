# Upload an Image #

## Introduction ##

With this method an image can be uploaded to a user's account.

Restrictions:
  * Max file size: 4MB
  * Accepted formats: JPG, GIF, PNG


## Uploading an Image ##

**Parameters**
  * oauth\_consumer\_key
  * oauth\_token
  * oauth\_signature\_method
  * oauth\_signature
  * oauth\_timestamp
  * oauth\_nonce
  * image
    * value: the file to be uploaded
  * content\_type
    * values:
      * adult
      * family
  * thumb\_format - _(optional)_
    * values:
      * JPG _(default)_
      * GIF
  * thumb\_size _(optional)_
    * values:
      * 100x100 _(default)_
      * 150x150
      * 180x180
      * 350x350
  * thumb\_cropping _(optional)_
    * values:
      * 0 _(aspect ratio will be preserved)_
      * 1 _(default - thumbnail will be cropped to a square)_
  * thumb\_info _(optional)_
    * values:
      * 0 _(default)_
      * 1 _(information about the image size and resolution will be appended to the thumbnail)_
  * gallery\_id _(optional)_
    * value:
      * 32 character long alphanumeric string identifying a user's gallery
  * response\_format  _(optional)_
    * values:
      * JSON _(default)_


**ATTENTION**<br>
Make sure that the image data is being posted correctly! (Content-Type: multipart/form-data)<br>

You MUST provide the user the option to pick the <b>content_type</b>.<br>
Please note that a maximum number of 500 images can be assigned to a gallery.<br>
<br>
The parameter <b>image</b> must be sent via POST method. All other parameters may be sent either via GET or POST method to following URL:<br>
<pre><code>http://www.imagebam.com/sys/API/resource/upload_image<br>
</code></pre>



Example <b>JSON</b> Response:<br>
<pre><code>{<br>
     "rsp": {<br>
          "stat": "ok", <br>
           "image": {<br>
                    "ID":"asdffd1234",<br>
                    "URL":"http://www.imagebam.com/image/asdffd1234",<br>
                    "thumbnail":"http://thumbnails20.imagebam.com/123/asdffd1234.jpg"<br>
                    "GID":"asdfasdfasdfasdfasdfasdfasdfasdf"<br>
           }<br>
     }<br>
}<br>
</code></pre>


The request can fail for the following reasons:<br>
<ul><li>invalid OAuth authentication<br>
</li><li>missing parameters<br>
</li><li>invalid parameters<br>
</li><li>invalid image size<br>
</li><li>invalid image format<br>
</li><li>image has been banned from ImageBam<br>
</li><li>internal error</li></ul>

In the case of an invalid OAuth authentication the response will be in the format required by the OAuth Core 1.0a.<br>
<br>
<h2>Error Codes</h2>
<ul><li><i>100</i> - no image was uploaded or the upload failed<br>
</li><li><i>101</i> - image size exceeds 4MB or file type not accepted<br>
</li><li><i>102</i> - image has been banned from ImageBam<br>
</li><li><i>102</i> - invalid or missing parameter: content_type<br>
</li><li><i>103</i> - invalid parameter: thumb_format<br>
</li><li><i>104</i> - invalid parameter: thumb_size<br>
</li><li><i>105</i> - invalid parameter: thumb_cropping<br>
</li><li><i>106</i> - invalid parameter: thumb_info<br>
</li><li><i>107</i> - invalid parameter: gallery_id<br>
</li><li><i>108</i> - permission denied: gallery_id<br>
</li><li><i>109</i> - gallery contains maximum of 500 images<br>
</li><li><i>110</i> - invalid parameter: response_format<br>
</li><li><i>111</i> - an internal error has occurred</li></ul>




<h2>Example Code</h2>
<h3>PHP using cURL</h3>
<pre><code>// required parameters for the OAuth-authentication<br>
// replace all parameters!<br>
$API_key = "XXXX6E6JXQUGXXXX";<br>
$API_secret = "XXXXLUJPLQU5KH3WM75SLBBI16YXXXX";<br>
$oauth_nonce = "123456";<br>
$oauth_token = "XXXXVVaKj1SmadeAmlU9IYBUOgVTXXXX";<br>
$oauth_token_secret = "XXXXaPYwaTESXXXX";<br>
$oauth_timestamp = time();<br>
$oauth_signature_method = "MD5";<br>
<br>
// build the signature string<br>
$oauth_signature_string = "";<br>
$oauth_signature_string .= $API_key;<br>
$oauth_signature_string .= $API_secret;<br>
$oauth_signature_string .= $oauth_timestamp;<br>
$oauth_signature_string .= $oauth_nonce;<br>
$oauth_signature_string .= $oauth_token;<br>
$oauth_signature_string .= $oauth_token_secret;<br>
<br>
// calculate the MD5-checksum of the signature string<br>
$oauth_signature = md5($oauth_signature_string);<br>
<br>
// populate an array with parameters<br>
$pvars   = array(<br>
        // parameters required by OAuth<br>
        "oauth_consumer_key" =&gt; $API_key,<br>
        "oauth_signature_method" =&gt; $oauth_signature_method,<br>
        "oauth_signature" =&gt; $oauth_signature,<br>
        "oauth_timestamp" =&gt; $oauth_timestamp,<br>
        "oauth_nonce" =&gt; $oauth_nonce,<br>
        "oauth_token" =&gt; $oauth_token,<br>
        <br>
        // optional parameters<br>
        "content_type" =&gt; "family",<br>
        "thumb_format" =&gt; "JPG",<br>
        "thumb_size" =&gt; "100x100",<br>
        "thumb_cropping" =&gt; 0,<br>
        "thumb_info" =&gt; 1,<br>
        "gallery_id" =&gt; "asdfasdfasdfasdfasdfasdfasdfasdf",<br>
        "response_format" =&gt; "JSON",<br>
        "image" =&gt; "@/path/to/file.jpg" // ATTENTION - note the @ infront of the filename!<br>
);<br>
<br>
// initialize curl and set parameters<br>
$curl    = curl_init();<br>
curl_setopt($curl, CURLOPT_URL, 'http://www.imagebam.com/sys/API/resource/upload_image');<br>
curl_setopt($curl, CURLOPT_TIMEOUT, 30);<br>
curl_setopt($curl, CURLOPT_POST, 1);<br>
curl_setopt($curl, CURLOPT_POSTFIELDS, $pvars);<br>
curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);<br>
<br>
// execute, get information and close connection<br>
$response = curl_exec($curl);<br>
$info = curl_getinfo($curl);<br>
curl_close ($curl);<br>
<br>
// check if the http-code is 200<br>
if($info['http_code'] != 200) {<br>
    exit("OAuth error: ".$response);<br>
}<br>
<br>
// decode the json-encoded response<br>
$response_array = json_decode($response);<br>
<br>
// print nicely<br>
print("&lt;pre&gt;");<br>
print_r($response_array);<br>
print("&lt;/pre&gt;");<br>
</code></pre>