
## Passive Information Gathering 

[Open-source Intelligence](https://osintframework.com/)

[Attack surface OWASP Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Attack_Surface_Analysis_Cheat_Sheet.html)

`whois` is a TCP service, tool, and type of database about a domain name

```bash
whois megacorpone.com -h 192.168.50.251
whois 38.100.193.70 -h 192.168.50.251
```

### Google Dorking
[_Google Hacking Database_](https://www.exploit-db.com/google-hacking-database)
[DorkSearch](https://dorksearch.com/)

```
site:megacorpone.com filetype:txt
ext:php/xml/py
-filetype:txt [ - excludes search results]
intitle:"index of" "parent directory"
```

[_Netcraft_](https://www.netcraft.com/) - discovering which tech is used on a website and which other hosts share the same IP netblock [DNS search page](https://searchdns.netcraft.com/)

### GitHub

```
owner:megacorpone path:users (search for any files with the word "users" in the filename)
```

[_Gitrob_](https://github.com/michenriksen/gitrob)
[_Gitleaks_](https://github.com/zricethezav/gitleaks)

### Shodan

[_Shodan_](https://www.shodan.io/) for IoT and other stuff

### Security Headers and SSL/TLS

[_Security Headers_](https://securityheaders.com/)

[Qualys SSL Labs](https://www.ssllabs.com/ssltest/)

[cipher suites](<https://www.ssllabs.com/ssltest/)

[_server hardening_](https://csrc.nist.gov/publications/detail/sp/800-123/final) NIST documenation

## Active Information Gathering

[_LOLBAS_](https://lolbas-project.github.io/) Windows binaries for post-compromised analysis

### DNS Enum

```shell
host www.megacoropne.com
host -t mx megacorpone.com
host -t txt megacorpone.com

for ip in $(cat list.txt); do host $ip.megacorpone.com; done
```

[PTR records](https://www.cloudflare.com/learning/dns/dns-records/dns-ptr-record/)

[_reverse lookups_](https://www.cloudflare.com/learning/dns/glossary/reverse-dns/)

```shell
for ip in $(seq 200 254); do host 51.222.169.$ip; done | grep -v "not found"
```

[_DNSRecon_](https://github.com/darkoperator/dnsrecon)

```shell
dnsrecon -d megaropone.com -t std
dnsrecon -d megacorpone.com -D ~/list.txt -t brt
```

```shell
kali:~$ dnsenum megacorpone.com
```


connect to our Windows 11 client. For this we can use the _xfreerdp_ command. The syntax is **/u:** and the username, **/p:** and the password, and **/v:** and the ip address.

```shell
kali@kali:~$ xfreerdp /u:student /p:lab /v:192.168.50.152
```

```powershell
C:\Users\student> nslookup mail.begacorptwo.com
C:\Users\student> nslookup -type=TXT info.megacorptwo.com 192.168.50.151
```

### TCP/UDP Port Scanning

```shell
nc -nvv -w 1 -z 192.168.50.152 3388-3390
nc -nv -u -z -w 1 192.168.50.149 120-123
```

## NMAP

[raw sockets](http://man7.org/linux/man-pages/man7/raw.7.html)

[Berkeley socket API](https://networkprogrammingnotes.blogspot.com/p/berkeley-sockets.html)

some fun with [_iptables_](http://netfilter.org/projects/iptables/index.html)

```shell
sudo iptables -I INPUT 1 -s 192.168.50.149 -j ACCEPT
sudo iptables -I OUTPUT 1 -d 192.168.50.149 -j ACCEPT
sudo iptables -Z
sudo iptables -vn -L
sudo iptables -Z
```

[_MASSCAN_](https://tools.kali.org/information-gathering/masscan)

[RustScan](https://rustscan.github.io/RustScan/)

```shell
sudo nmap -sS 192.168.50.149
nmap -sT 192.168.50.149
sudo nmap -sU 192.168.50.149
sudo nmap -sU -sS 192.168.50.149
nmap -sn 192.168.50.1-253
nmap -v -sn 192.168.50.1-253 -oG ping-sweep.txt
grep Up ping-sweep.txt | cut -d " " -f 2
nmap -p 80 192.168.50.1-253 -oG web-sweep.txt
nmap -sT -A --top-ports=20 192.168.50.1-253 -oG top-port-sweep.txt
sudo nmap -O 192.168.50.14 --osscan-guess
nmap -sT -A 192.168.50.14
nmap --script-help http-headers
```

[NSE](http://nmap.org/book/nse.html)

```shell
/usr/share/nmap/nmap-services
/usr/share/nmap/scripts
```

[_Test-NetConnection_](https://docs.microsoft.com/en-us/powershell/module/nettcpip/test-netconnection?view=windowsserver2022-ps)

```powershell
C:\Users\student> Test-NetConnection -Port 445 192.168.50.151
C:\Users\student> 1..1024 | % {echo ((New-Object Net.Sockets.TcpClient).Connect("192.168.50.151", $_)) "TCP port $_ is open"} 2>$null
```

### SMB Enumeration

```shell
nmap -v -p 139,445 -oG smb.txt 192.168.50.1-254
sudo nbtscan -r 192.168.50.0/24
ls -1 /usr/share/nmap/scripts/smb*
nmap -v -p 139,445 --script smb-os-discovery 192.168.50.152
```

```powershell
C:\Users\student>net view \\dc01 /all
```

### SMTP Enumeration

```shell
nc -nv 192.168.50.8 25
VRFY root
VRFY idontexist
^C
```

```python
#!/usr/bin/python

import socket
import sys

if len(sys.argv) != 3:
        print("Usage: vrfy.py <username> <target_ip>")
        sys.exit(0)

# Create a Socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Connect to the Server
ip = sys.argv[2]
connect = s.connect((ip,25))

# Receive the banner
banner = s.recv(1024)

print(banner)

# VRFY a user
user = (sys.argv[1]).encode()
s.send(b'VRFY ' + user + b'\r\n')
result = s.recv(1024)

print(result)

# Close the socket
s.close()
```

```shell
python3 smtp.py root 192.168.50.8
python3 smtp.py johndoe 192.168.50.8
```

```powershell
C:\Users\student> Test-NetConnection -Port 25 192.168.50.8
C:\Users\student> dism /online /Enable-Feature /FeatureName:TelnetClient
C:\Windows\system32>telnet 192.168.50.8 25
```

### SNMP Enumeration

[IBM Knowledge Center](https://www.ibm.com/support/knowledgecenter/ssw_aix_71/commprogramming/mib.html)

[_OID_](https://www.ibm.com/docs/en/i/7.2?topic=schema-object-identifier-oid)

[_onesixtyone_](http://www.phreedom.org/software/onesixtyone/)


> Table 1 - Windows SNMP MIB values

| 1.3.6.1.2.1.25.1.6.0   | System Processes |
| ---------------------- | ---------------- |
| 1.3.6.1.2.1.25.4.2.1.2 | Running Programs |
| 1.3.6.1.2.1.25.4.2.1.4 | Processes Path   |
| 1.3.6.1.2.1.25.2.3.1.4 | Storage Units    |
| 1.3.6.1.2.1.25.6.3.1.2 | Software Name    |
| 1.3.6.1.4.1.77.1.2.25  | User Accounts    |
| 1.3.6.1.2.1.6.13.1.3   | TCP Local Ports  |


```shell
sudo nmap -sU --open -p 161 192.168.50.1-254 -oG open-snmp.txt

echo public > community
echo private >> community
echo manager >> community
for ip in $(seq 1 254); do echo 192.168.50.$ip; done > ips
onesixtyone -c community -i ips

snmpwalk -c public -v1 -t 10 192.168.50.

snmpwalk -c public -v1 192.168.50.151 1.3.6.1.4.1.77.1.2.25

snmpwalk -c public -v1 192.168.50.151 1.3.6.1.2.1.25.4.2.1.2

snmpwalk -c public -v1 192.168.50.151 1.3.6.1.2.1.25.6.3.1.2

snmpwalk -c public -v1 192.168.50.151 1.3.6.1.2.1.6.13.1.3
```

