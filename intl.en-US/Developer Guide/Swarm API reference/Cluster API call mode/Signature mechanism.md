# Signature mechanism {#concept_rw3_mxd_xdb .concept}

## Introduction {#section_ylc_pxd_xdb .section}

The Access Key ID and Access Key Secret are officially issued to you by Alibaba Cloud \(you can apply for and manage them on the Alibaba Cloud official website\). The Access Key ID is used to identify your identity. The Access Key Secret is the key used to encrypt the signature string and verify the signature string on the server side. You must keep the Access Key Secret confidential. Only you and Alibaba Cloud can know it.

Container Service verifies each access request it receives. Therefore, all requests sent to Container Service must contain signature information. Container Service performs symmetric encryption by using the Access Key ID and Access Key Secret to verify the identity of request senders. If the calculated verification code is the same as the one provided, the request is considered as valid. Otherwise, Container Service rejects the request and returns the HTTP 403 error.

You can add the authorization header in the HTTP request to contain the signature information, indicating that the message has been authorized.

Container Service requires to contain the signature in the HTTP header in the format of `Authorization: acs [Access Key ID]:[Signature]`.

The `Signature` calculation method is as follows:

```
Signature = base64(hmac-sha1(VERB + "\n"
            + ACCEPT + "\n" +
            + Content-MD5 + "\n"
            + Content-Type + "\n"
            + Date + "\n"
            + CanonicalizedHeaders + "\n"
            + CanonicalizedResource))
```

-   `VERB` indicates the HTTP method, for example, `PUT`.
-   `Accept` indicates the return type required by the client, which can be application/json or application/xml.
-   `Content-MD5` indicates the MD5 value of the requested content.
-   `Content-Type` indicates the type of the requested content.
-   `Date` indicates the operation time, which cannot be null. Currently, only the GMT format is supported. If the difference between the request time and the CAS server time exceeds 15 minutes, CAS considers the request as invalid and returns error 400. For more information, see the 5th section. For example, `Thu, 17 Mar 2012 18:49:58 GMT`.
-   `CanonicalizedHeaders` indicates a combination of fields started with `x-acs-` in the HTTP request.
-   `CanonicalizedResource` indicates the uniform resource identifier \(URI\) of the resource in the HTTP request. For example, `/clusters? name=my-clusters&resource=new`.

**Note:** Conform to the following specifications for `CanonicalizedHeaders` \(headers started with `x-acs-`\) before signature verification:

1.  Convert the names of all HTTP request headers started with `x-acs-` to lowercase letters. For example, convert `X-ACS-Meta-Name: TaoBao` to `x-acs-meta-name: TaoBao`. The names of request headers are case-insensitive according to Alibaba Cloud specifications. However, we recommend that you use the lowercase letters.
2.  If the value part of a public request header is too long, replace the `\t`, `\n`, `\r`, and `\f` separators with spaces.
3.  Sort all HTTP request headers that are obtained from the preceding step and compliant with Alibaba Cloud specifications in the lexicographic ascending order.
4.  Delete any space at either side of a separator between request header and content. For example, convert `x-acs-meta-name: TaoBao,Alipay` to `x-acs-meta-name:TaoBao,Alipay`.
5.  Separate all headers and contents with the `\n` separator to form the final `CanonicalizedHeaders`.

**Note:** The format specification for `CanonicalizedResource`: `CanonicalizedResource` indicates the standard description of the resource you want to access. Sort sub-resources and query in the lexicographically ascending order and separate them by using the `&` separator to generate a sub-resource string \(all parameters after `?`\).

```
http://cs.aliyuncs.com/clusters?name=my-clusters&resource=new
```

The `CanonicalizedResource` format is:

```
/clusters? name=my-clusters&resource=new
```

## Signature example {#section_dlb_qyd_xdb .section}

**Overview**

The following example shows the signature process.

In the example, the Access Key ID and Access Key Secret are `access_key_id` and `access_key_secret` respectively. We recommend that you use your own API call program to calculate the signature string in the following example. Then, compare your signature string with the example result.

The request example is as follows:

```
POST http://cs.aliyuncs.com/clusters?param1=value1&param2=value2 HTTP/1.1
Accept-Encoding: identity
Content-Length: 210
Content-MD5: 6U4ALMkKSj0PYbeQSHqgmA==    
x-acs-version: 2015-12-15 
Accept: application/json
User-Agent: cs-sdk-python/0.0.1 (Darwin/15.2.0/x86_64;2.7.10)
x-acs-signature-nonce: fbf6909a-93a5-45d3-8b1c-3e03a7916799
x-acs-signature-version: 1.0
Date: Wed, 16 Dec 2015 12:20:18 GMT
x-acs-signature-method: HMAC-SHA1
Content-Type: application/json;charset=utf-8
X-Acs-Region-Id: cn-beijing  
Authorization: acs access_key_id:/ZmVlMDNkNDA1ZTQyMWViYWY1MTRhZGVjODgxMDM4YzRiMzEzNTg0ZA==
{"password": "Just$test","instance_type": "ecs.m2.medium","name": "my-test-cluster-97082734","size": 1,"network_mode": "classic","data_disk_category": "cloud","data_disk_size": 10,"ecs_image_id": "m-253llee3l"}
```

**Request construction process**

Calculate `Content-Length` and `Content-MD5`

`Content-Length`: The length of the body content.

**Note:** No space or line break is at the beginning of the example body.

```
body: {"password": "Just$test","instance_type": "ecs.m2.medium","name": "my-test-cluster-97082734","size": 1,"network_mode": "classic","data_disk_category": "cloud","data_disk_size": 10,"ecs_image_id": "m-253llee3l"}
Content-Length: 210
```

`Content-MD5`: The MD5 calculation process.

```
body: {"password": "Just$test","instance_type": "ecs.m2.medium","name": "my-test-cluster-97082734","size": 1,"network_mode": "classic","data_disk_category": "cloud","data_disk_size": 10,"ecs_image_id": "m-253llee3l"}
# Calculate the MD5 value of the body.
md5(body): e94e002cc90a4a3d0f61b790487aa098
# Convert the MD5 value to a byte array. Convert every two hexadecimal symbols of the MD5 value to a byte.
# For example, e9 -> 11111111111111111111111111101001 -> -23
bytes(md5(body)): {[-23], [78], [0], [44], [-55], [10], [74], [61], [15], [97], [-73], [-112], [72], [122], [-96], [-104]}
# Convert the obtained byte array to a Base64 string.
base64(bytes(md5(body))): 6U4ALMkKSj0PYbeQSHqgmA==
Content-MD5: 6U4ALMkKSj0PYbeQSHqgmA==
```

Process `CanonicalizedHeaders`

```
# List all headers started with 'x-acs-'.
x-acs-version: 2015-12-15  
x-acs-signature-nonce: ca480402-7689-43ba-acc4-4d2013d9d8d4
x-acs-signature-version: 1.0
x-acs-signature-method: HMAC-SHA1
X-Acs-Region-Id: cn-beijing
# Convert the request name to lowercase letters, delete the spaces at the beginning and end of each line, and sort the headers in the lexicographically ascending order. Delete any space at either side of a separator between request header and content. 
# Note: No line break is in the last line.
x-acs-region-id:cn-beijing
x-acs-signature-method:HMAC-SHA1
x-acs-signature-nonce:fbf6909a-93a5-45d3-8b1c-3e03a7916799
x-acs-signature-version:1.0
x-acs-version:2015-12-15
```

Calculate `CanonicalizedResource`

In the example, the length of CanonicalizedResource is 27.

**Note:** An `\n` line break is at the end of the first line.

```
/clusters? param1=value1&param2=value2
```

Calculate `Signature`

Assemble `SignatureString`. In the example, the length of the signature string is 307. An `\n` line break is at the end of all lines except the last line.

```
POST
application/json
6U4ALMkKSj0PYbeQSHqgmA==
application/json;charset=utf-8
Wed, 16 Dec 2015 12:20:18 GMT
x-acs-region-id:cn-beijing
x-acs-signature-method:HMAC-SHA1
x-acs-signature-nonce:fbf6909a-93a5-45d3-8b1c-3e03a7916799
x-acs-signature-version:1.0
x-acs-version:2015-12-15
/clusters? param1=value1&param2=value2
```

Calculate `Signature`

```
# Use Access Key Secret to encrypt the signature string. In the example, the accessKeySecret is access_key_secret.
hmac-sha1(SignatureString): fee03d405e421ebaf514adec881038c4b313584d
# Convert the encrypted string to a byte array, similar to the Content-MD5 calculation method.
# Convert the byte array into a Base64 string to get the final signature string.
base64(bytes(hmac-sha1(SignatureString))): ZmVlMDNkNDA1ZTQyMWViYWY1MTRhZGVjODgxMDM4YzRiMzEzNTg0ZA==
Signature: ZmVlMDNkNDA1ZTQyMWViYWY1MTRhZGVjODgxMDM4YzRiMzEzNTg0ZA==
```

Finish

After the preceding processing, add some other header information to construct the final HTTP request as follows:

```
POST http://cs.aliyuncs.com/clusters?param1=value1&param2=value2 HTTP/1.1
Accept-Encoding: identity
Content-Length: 210
Content-MD5: 6U4ALMkKSj0PYbeQSHqgmA==    
x-acs-version: 2015-12-15 
Accept: application/json
User-Agent: cs-sdk-python/0.0.1 (Darwin/15.2.0/x86_64;2.7.10)
x-acs-signature-nonce: fbf6909a-93a5-45d3-8b1c-3e03a7916799
x-acs-signature-version: 1.0
Date: Wed, 16 Dec 2015 12:20:18 GMT
x-acs-signature-method: HMAC-SHA1
Content-Type: application/json;charset=utf-8
X-Acs-Region-Id: cn-beijing  
Authorization: acs access_key_id:/ZmVlMDNkNDA1ZTQyMWViYWY1MTRhZGVjODgxMDM4YzRiMzEzNTg0ZA==
{"password": "Just$test","instance_type": "ecs.m2.medium","name": "my-test-cluster-97082734","size": 1,"network_mode": "classic","data_disk_category": "cloud","data_disk_size": 10,"ecs_image_id": "m-253llee3l"}
```

