# Spring Boot + Oauth2 + CORS Example

The purpose of this example is to demonstrate Spring Boot 1.4.2 with Oauth2 (using JWTs) and CORS support.

But at this time the CORS support is not working. See [this GitHub issue](https://github.com/spring-projects/spring-security-oauth/issues/938).

## Run using Gradle:

```
cd {repo-root}
gradle bootRun
```

## Test Direct Requests (Works)

Use Postman or Httpie and it'll respond perfectly, for example:

```
http --auth-type basic --auth client1:password --form POST http://localhost:8080/oauth/token grant_type="password" username="user1" password="password"

HTTP/1.1 200 
Cache-Control: no-store
Content-Type: application/json;charset=UTF-8
Date: Fri, 23 Dec 2016 03:15:25 GMT
Pragma: no-cache
Transfer-Encoding: chunked
X-Application-Context: application
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block

{
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyLXV1aWQiOiI3YzNmNTY3My1jZWRlLTRmYjMtOGUzZC05YjdlMGZhODA0OGYiLCJ1c2VyX25hbWUiOiJ1c2VyMSIsInNjb3BlIjpbImZvby1zY29wZSJdLCJleHAiOjE0ODI0NjM1MjUsImF1dGhvcml0aWVzIjpbInJpZ2h0MSJdLCJqdGkiOiI4OTJkM2JhZC01YTFmLTQ0NTUtOWNhNS1jNjM2MWUwMTg2MjYiLCJjbGllbnRfaWQiOiJjbGllbnQxIn0.ofNkDHhfpFwdW5qcgxoFzpoY9wzvs7vwwH2ULxBm4Bc",
    "expires_in": 599,
    "jti": "892d3bad-5a1f-4455-9ca5-c6361e018626",
    "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyLXV1aWQiOiI3YzNmNTY3My1jZWRlLTRmYjMtOGUzZC05YjdlMGZhODA0OGYiLCJ1c2VyX25hbWUiOiJ1c2VyMSIsInNjb3BlIjpbImZvby1zY29wZSJdLCJhdGkiOiI4OTJkM2JhZC01YTFmLTQ0NTUtOWNhNS1jNjM2MWUwMTg2MjYiLCJleHAiOjE0ODI0NjQ3MjUsImF1dGhvcml0aWVzIjpbInJpZ2h0MSJdLCJqdGkiOiJkNjI2NmNkNS1mZjdkLTRjYzItOWJjMS1jODU2MmEwOTY2ZGIiLCJjbGllbnRfaWQiOiJjbGllbnQxIn0.H_mKswPz8zuEoQajiO4FvrnFXJoVZttqXFG3sP58N4I",
    "scope": "foo-scope",
    "token_type": "bearer",
    "user-uuid": "7c3f5673-cede-4fb3-8e3d-9b7e0fa8048f"
}
```

## Test JavaScript Chrome Requests (Doesn't Currently Work)

When you try to use JavaScript in Chrome it fails with a:
```
XMLHttpRequest cannot load http://localhost:8080/oauth/token. 
Response for preflight has invalid HTTP status code 401
```

Here is the full request from Chome:
```
OPTIONS /oauth/token HTTP/1.1
Host: localhost:8080
Connection: keep-alive
Access-Control-Request-Method: POST
Origin: http://localhost:9000
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.95 Safari/537.36
Access-Control-Request-Headers: authorization, cache-control
Accept: */*
Referer: http://localhost:9000/
Accept-Encoding: gzip, deflate, sdch, br
Accept-Language: en-US,en;q=0.8
```
And the full response Chrome receives back:
```
HTTP/1.1 401
WWW-Authenticate: Basic realm="oauth2/client"
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
X-Frame-Options: DENY
Access-Control-Allow-Origin: http://localhost:9000
Vary: Origin
Access-Control-Allow-Methods: POST
Access-Control-Allow-Headers: authorization, cache-control
Access-Control-Allow-Credentials: true
Allow: GET, HEAD, POST, PUT, DELETE, TRACE, OPTIONS, PATCH
Content-Length: 0
Date: Fri, 23 Dec 2016 03:09:45 GMT
```

For the JavaScript in Chrome test, simply running Spring Boot on a separate port or use your favorite webserver, such that it hosts the following `index.html`:
```
<!DOCTYPE html>
<html>
<head>
<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js"></script>
<script type="text/javascript">

var form = new FormData();
form.append("grant_type", "password");
form.append("username", "user1");
form.append("password", "password");

var settings = {
  "async": true,
  "crossDomain": true,
  "url": "http://localhost:8080/oauth/token",
  "method": "POST",
  "headers": {
    "authorization": "Basic Y2xpZW50MTpwYXNzd29yZA==",
    "cache-control": "no-cache"
  },
  "processData": false,
  "contentType": false,
  "mimeType": "multipart/form-data",
  "data": form
}

$.ajax(settings).done(function (response) {
  console.log(response);
});
</script>
</head>

<body>
Check the console.
</body>
</html>
```
