# Common parameters {#reference_c4w_x21_wdb .reference}

## Public request headers {#section_mr5_lf1_wdb .section}

Public request parameters are the request parameters that each interface must use.

|Parameter|Description|Options|
|:--------|:----------|:------|
|Authorization |The authentication information used to verify the validity of a request. Format: `AccessKeyId:Signature`.|Required|
|Content-Length|The content length of an HTTP request, which is defined in RFC 2616.|Required|
|Content-Type|The content type of an HTTP request, which is defined in RFC 2616.|Required|
|Content-MD5|The Base64-encoded results converted from 128-bit MD5 hash value of the HTTP message body. We recommend that you add this message to all requests to prevent requests from being tampered.|Required|
|Date|The construction time of a request. Currently, only the GMT format is supported. If the difference between the construction time and the MNS server time exceeds 15 minutes, invalid request is returned.|Required|
|Host|The host access value, for example, diku.aliyuncs.com.|Required|
|Accept|The return type required by the client. application/json and application/xml are supported.|Required|
|x-acs-version|The API version. The current version is 2015-12-15.|Required|
|x-acs-region-id|A region indicates the physical location of an Elastic Compute Service \(ECS\) instance.|Required|
|x-acs-signature-nonce|The unique random number used to prevent network replay attacks. Different random numbers must be used for different requests.|Required|
|x-acs-signature-method|The method of user signature. Currently only HMAC-SHA1 is supported.|Required|

**Examples**

```
GET /clusters HTTP/1.1
Host: cs.aliyuncs.com
Accept: application/json
User-Agent: cs-sdk-python/0.0.1 (Darwin/15.2.0/x86_64;2.7.10)
x-acs-signature-nonce: f63659d4-10ac-483b-99da-ea8fde61eae3
Authorization: acs <yourAccessKeyId>:<yourSignature>
x-acs-signature-version: 1.0
Date: Wed, 16 Dec 2015 11:18:47 GMT
x-acs-signature-method: HMAC-SHA1
Content-Type: application/json;charset=utf-8
X-Acs-Region-Id: cn-beijing
Content-Length: 0
```

## Public response headers {#section_zr5_lf1_wdb .section}

Each time you send a request to call an interface, the system returns a unique identifier \(RequestId\), no matter the request is successful or not.

**Examples**

XML example:

```
<? xml version="1.0" encoding="UTF-8"? >
<!—Result Root Node-->
<Interface name+Response>
 | <!—Return request tag-->
 | <RequestId>4C467B38-3910-447D-87BC-AC049166F216</RequestId>
 | <!—Return result data-->
</Interface name+Response>
```

JSON example:

```
{
    "RequestId": "4C467B38-3910-447D-87BC-AC049166F216"
    /* Response data */
}
```

