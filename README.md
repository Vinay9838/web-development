# Web Development Questions & Answers

---

---
### Table of Contents

| No. | Questions                                                                                |
|-----|------------------------------------------------------------------------------------------|
|     | **[Cross-Origin Resource Sharing (CORS)](#CORS)**                                        |
| 1   | [What is CORS?](#What-is-CORS?)                                                          |
| 2   | [On which mechanism CORS rely upon?](#On-which-mechanism-CORS-rely-upon)                 |
| 3   | [Give an example of CORS.](#Give-an-example-of-CORS)                                     |
| 4   | [What requests use CORS?](#What-requests-use-CORS)                                       |
| 5   | [What is simple requests?](#What-is-simple-requests)                                     |
| 6   | [Explain simple requests with example.](#Explain-simple-requests-with-example)           |
| 7   | [Explain Preflighted requests with example.](#Explain-Preflighted-requests-with-example) |
| 8   | [Explain Requests with credentials.](#Explain-Requests-with-credentials)                 |
| 9   | [Explain different HTTP response Headers.](#Explain-differend-HTTP-response-Headers)     |
| 10  | [Explain different HTTP request Headers.](#Explain-differend-HTTP-request-Headers)       |




## Cross-Origin Resource Sharing (CORS)


1. ### What is CORS?
   Cross-Origin Resource Sharing (CORS) is an HTTP-header based mechanism that allows a server to indicate any origins (domain, scheme, or port) other than its own from which a browser should permit loading resources

   **[⬆ Back to Top](#table-of-contents)**

2. ### On which mechanism CORS rely upon?
    CORS also relies on a mechanism by which browsers make a "preflight" request to the server hosting the cross-origin resource, in order to check that the server will permit the actual request.
    
   **[⬆ Back to Top](#table-of-contents)**

3. ### Give an example of CORS.
    The front-end JavaScript code served from https://domain-a.com uses XMLHttpRequest to make a request for https://domain-b.com/data.json. XMLHttpRequest and the Fetch API follow the same-origin policy. This means that a web application using those APIs can only request resources from the same origin the application was loaded from unless the response from other origins includes the right CORS headers.

   **[⬆ Back to Top](#table-of-contents)**

4. ### What requests use CORS?
    * Invocations of the XMLHttpRequest or Fetch APIs, as discussed above.
    * Web Fonts (for cross-domain font usage in @font-face within CSS), so that servers can deploy TrueType fonts that can only be loaded cross-origin and used by web sites that are permitted to do so.
    * WebGL textures.
    * Images/video frames drawn to a canvas using drawImage().
    * CSS Shapes from images.
   

   **[⬆ Back to Top](#table-of-contents)**

5. ### What is simple requests?
    Some requests don't trigger a CORS preflight. Those are called simple requests.A simple request is one that meets all the following conditions:
    * One of the allowed methods:
      * GET
      * HEAD
      * POST
    * The only headers which are allowed to be manually set are - 
      * Accept
      * Accept-Language
      * Content-Language
      * Content-Type (please note the additional requirements below)
      * Range (only with a simple range header value; e.g., bytes=256- or bytes=127-255)
    * The only type/subtype combinations allowed for the media type specified in the Content-Type header are:
      * application/x-www-form-urlencoded
      * multipart/form-data
      * text/plain
    * If the request is made using an XMLHttpRequest object, no event listeners are registered on the object returned by the XMLHttpRequest.Upload property used in the request.
    * No ReadableStream object is used in the request.
   

   **[⬆ Back to Top](#table-of-contents)**

6. ### Explain simple requests with example?
   Suppose web content at https://foo.example wishes to invoke content on domain https://bar.other
   
   #### foo.example
```shell
  const xhr = new XMLHttpRequest();
  const url = 'https://bar.other/resources/public-data/';
  xhr.open('GET', url);
  xhr.onreadystatechange = someHandler;
  xhr.send();
```

![Home Page](https://github.com/Vinay9838/web-development/blob/master/media/simple-req.png?raw=true)

   Let's look at what the browser will send to the server in this case:

```shell
GET /resources/public-data/ HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:71.0) Gecko/20100101 Firefox/71.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Connection: keep-alive
Origin: https://foo.example
```
   The request header of note is Origin, which shows that the invocation is coming from https://foo.example.
   Now let's see how the server responds:

```shell
HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 00:23:53 GMT
Server: Apache/2
Access-Control-Allow-Origin: *
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Transfer-Encoding: chunked
Content-Type: application/xml

[…XML Data…]
```
   In response, the server returns a `Access-Control-Allow-Origin` header with Access-Control-Allow-Origin: *, which means that the resource can be accessed by any origin.
   If the resource owners at https://bar.other wished to restrict access to the resource to requests only from https://foo.example.

`Access-Control-Allow-Origin: https://foo.example`

   **[⬆ Back to Top](#table-of-contents)**

7. ### Explain Preflighted requests with example.
    Unlike simple requests, for "preflighted" requests the browser first sends an HTTP request using the OPTIONS method to the resource on the other origin, in order to determine if the actual request is safe to send. Such cross-origin requests are preflighted.
    
    The following is an example of a request that will be preflighted:
```shell
const xhr = new XMLHttpRequest();
xhr.open('POST', 'https://bar.other/resources/post-here/');
xhr.setRequestHeader('X-PINGOTHER', 'pingpong');
xhr.setRequestHeader('Content-Type', 'application/xml');
xhr.onreadystatechange = handler;
xhr.send('<person><name>Arun</name></person>');

```

![Home Page](https://github.com/Vinay9838/web-development/blob/master/media/preflight_correct.png?raw=true)

   Let's look at the full exchange between client and server. The first exchange is the preflight request/response:

```shell
OPTIONS /doc HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:71.0) Gecko/20100101 Firefox/71.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Connection: keep-alive
Origin: https://foo.example
Access-Control-Request-Method: POST
Access-Control-Request-Headers: X-PINGOTHER, Content-Type

HTTP/1.1 204 No Content
Date: Mon, 01 Dec 2008 01:15:39 GMT
Server: Apache/2
Access-Control-Allow-Origin: https://foo.example
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: X-PINGOTHER, Content-Type
Access-Control-Max-Age: 86400
Vary: Accept-Encoding, Origin
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive

```
   The server responds with Access-Control-Allow-Origin: https://foo.example, restricting access to the requesting origin domain only. It also responds with Access-Control-Allow-Methods, which says that POST and GET are valid methods to query the resource in question (this header is similar to the Allow response header, but used strictly within the context of access control).
   
   Finally, Access-Control-Max-Age gives the value in seconds for how long the response to the preflight request can be cached without sending another preflight request. The default value is 5 seconds. In the present case, the max age is 86400 seconds (= 24 hours). Note that each browser has a maximum internal value that takes precedence when the Access-Control-Max-Age exceeds it.

   Once the preflight request is complete, the real request is sent:

```shell
POST /doc HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:71.0) Gecko/20100101 Firefox/71.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Connection: keep-alive
X-PINGOTHER: pingpong
Content-Type: text/xml; charset=UTF-8
Referer: https://foo.example/examples/preflightInvocation.html
Content-Length: 55
Origin: https://foo.example
Pragma: no-cache
Cache-Control: no-cache

<person><name>Arun</name></person>

HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:15:40 GMT
Server: Apache/2
Access-Control-Allow-Origin: https://foo.example
Vary: Accept-Encoding, Origin
Content-Encoding: gzip
Content-Length: 235
Keep-Alive: timeout=2, max=99
Connection: Keep-Alive
Content-Type: text/plain

[Some XML payload]

```

    
   **[⬆ Back to Top](#table-of-contents)**

3. ### Explain Requests with credentials.
    The most interesting capability exposed by both XMLHttpRequest or Fetch and CORS is the ability to make "credentialed" requests that are aware of HTTP cookies and HTTP Authentication information. By default, in cross-origin XMLHttpRequest or Fetch invocations, browsers will not send credentials. A specific flag has to be set on the XMLHttpRequest object or the Request constructor when it is invoked.

    In this example, content originally loaded from https://foo.example makes a simple GET request to a resource on https://bar.other which sets Cookies. Content on foo.example might contain JavaScript like this:

```shell
const invocation = new XMLHttpRequest();
const url = 'https://bar.other/resources/credentialed-content/';

function callOtherDomain() {
  if (invocation) {
    invocation.open('GET', url, true);
    invocation.withCredentials = true;
    invocation.onreadystatechange = handler;
    invocation.send();
  }
}

```

![Home Page](https://github.com/Vinay9838/web-development/blob/master/media/cred-req-updated.png?raw=true)

   Here is a sample exchange between client and server:

```shell
GET /resources/credentialed-content/ HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:71.0) Gecko/20100101 Firefox/71.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Connection: keep-alive
Referer: https://foo.example/examples/credential.html
Origin: https://foo.example
Cookie: pageAccess=2

HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:34:52 GMT
Server: Apache/2
Access-Control-Allow-Origin: https://foo.example
Access-Control-Allow-Credentials: true
Cache-Control: no-cache
Pragma: no-cache
Set-Cookie: pageAccess=3; expires=Wed, 31-Dec-2008 01:34:53 GMT
Vary: Accept-Encoding, Origin
Content-Encoding: gzip
Content-Length: 106
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain

[text/plain payload]

```


   **[⬆ Back to Top](#table-of-contents)**

2. ### Explain different HTTP response Headers.
    **Access-Control-Allow-Origin** -
      A returned resource may have one Access-Control-Allow-Origin header with the following syntax:
```shell
Access-Control-Allow-Origin: <origin> | *
```
   Access-Control-Allow-Origin specifies either a single origin which tells browsers to allow that origin to access the resource; or else — for requests without credentials — the "*" wildcard tells browsers to allow any origin to access the resource.

   **Access-Control-Expose-Headers** - 
      The Access-Control-Expose-Headers header adds the specified headers to the allowlist that JavaScript (such as getResponseHeader()) in browsers is allowed to access.

```shell
Access-Control-Expose-Headers: X-My-Custom-Header, X-Another-Custom-Header
```

   **Access-Control-Max-Age** -
      The Access-Control-Max-Age header indicates how long the results of a preflight request can be cached. For an example of a preflight request, see the above examples.

```shell
Access-Control-Max-Age: <delta-seconds>
```

   **Access-Control-Allow-Credentials** -
      The Access-Control-Allow-Credentials header indicates whether or not the response to the request can be exposed when the credentials flag is true

```shell
Access-Control-Allow-Credentials: true
```

   **Access-Control-Allow-Methods** - 
      The Access-Control-Allow-Methods header specifies the method or methods allowed when accessing the resource. This is used in response to a preflight request.

```shell
Access-Control-Allow-Methods: <method>[, <method>]*
```

   **Access-Control-Allow-Headers** -
      The Access-Control-Allow-Headers header is used in response to a preflight request to indicate which HTTP headers can be used when making the actual request. This header is the server side response to the browser's Access-Control-Request-Headers header.

```shell
Access-Control-Allow-Headers: <header-name>[, <header-name>]*
```

    
   **[⬆ Back to Top](#table-of-contents)**

10. ### Explain different HTTP request Headers.
   **Origin** -
      The Origin header indicates the origin of the cross-origin access request or preflight request.

```shell
Origin: <origin>
```
   The origin is a URL indicating the server from which the request is initiated. It does not include any path information, only the server name.

   **Access-Control-Request-Method** -
      The Access-Control-Request-Method is used when issuing a preflight request to let the server know what HTTP method will be used when the actual request is made.

```shell
Access-Control-Request-Method: <method>
```

   **Access-Control-Request-Headers** -
      The Access-Control-Request-Headers header is used when issuing a preflight request to let the server know what HTTP headers will be used when the actual request is made

```shell
Access-Control-Request-Headers: <field-name>[, <field-name>]*
```

   eg - 

```shell
xhr.setRequestHeader('Content-Type', 'application/xml');
```

   **[⬆ Back to Top](#table-of-contents)**
   

