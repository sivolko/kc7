---
icon: '1'
---

# Section 1: Maybe It's just a tadpole?

1. What is the MITRE ATT\&CK ID for defacement?

Ans:  T1491

2. Who is the Web Administrator? (Paste the full name.)

Ans:  Anita Bath&#x20;

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

```kusto
Employees
| where role == 'Web Administrator'
| project name


```

3. What is the hostname of the Web Administrator machine?

Ans: ✅ MYZB-LAPTOP&#x20;

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

```kusto
Employees
| where role == 'Web Administrator'
| project hostname
```

4. When did the defacement happen exactly? (Paste the full timestamp.

Ans:  ✅ 2024-07-10T11:45:50Z

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

```kusto
ProcessEvents
| where hostname == "MYZB-LAPTOP"
| where process_commandline has "Shadow Truth"
| project timestamp


```

5. When was the first image uploaded? (Paste the full timestamp.

Ans:  ✅ 2024-07-10T10:53:37Z

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

```kusto
ProcessEvents
| where hostname == "MYZB-LAPTOP"
| where process_commandline contains "frog_mall_meme"
| sort by timestamp desc 
```

6. What is the Sha256 hash of the first meme that was uploaded to the webserver?

Ans:  ✅ 9880c2d74afb2e57c7de7b9d6d0976112887502bb80344d35df34e774628dba0

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

```kusto
FileCreationEvents
| where hostname == "MYZB-LAPTOP"
| where filename startswith "frog_mall_meme"
| sort by filename asc 
| project sha256,  filename
```

7. What domain were the images downloaded from?

Ans:  ✅ ronniesdankmemes.com

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Let's find out Anita Ip&#x20;

```kusto
Employees
| where role == 'Web Administrator'
| project ip_addr

Ans: 10.10.0.8
```

```kusto
OutboundNetworkEvents
| where src_ip == "10.10.0.8"
| where url contains "frog_mall_meme"
| extend domain = parse_url(url).Host
| distinct tostring(domain)
```

8. Which command did the attacker use to look for files containing passwords?

Ans:  ✅ Get-ChildItem -Path C:\Users\anbath\Documents\* -Include _password_ -Recurse

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

```kusto
ProcessEvents
| where hostname =="MYZB-LAPTOP"
| where process_commandline has "password"
| project process_commandline
```

9. What is the name of the file containing passwords?

Ans:  ✅ mypasswordsnstuff.txt

Let's modify the previous query, include contains&#x20;

```kusto
ProcessEvents
| where hostname =="MYZB-LAPTOP"
| where process_commandline contains "password"


```

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

11. What is the name of that domain?

Ans:  ✅ newdevelopmentupdates.org

12. What is the last IP address that the domain you found in Q11 resolve to?

Ans:  ✅ 239.72.6.37

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

```kusto
PassiveDns
| where domain == 'newdevelopmentupdates.org'




```

13. Do the IPs found in Q11 resolve to other domains? If they do, answer with the domain. If not, type no

Ans:  ✅ greenprojectnews.net

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

```kusto
PassiveDns
| where domain == "newdevelopmentupdates.org"
| distinct ip 
| lookup PassiveDns on ip 
| distinct domain
```

14. &#x20;What version of Firefox is the threat actor using?

Ans:  ✅ 3.6.11\


Let's find out the username of anita&#x20;

```json
anbath
```

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

```kusto
let TA_ips =
PassiveDns
| where domain == 'newdevelopmentupdates.org'
| distinct ip;
AuthenticationEvents
| where src_ip in (TA_ips)
| where username == 'anbath'
```

15. What is Anita’s email address?

Ans:  ✅ anita\_bath@framtidxdevcorp.com



<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Employees | where name == 'Anita Bath' | distinct email\_addr



16. What is the subject of the email she received?

Ans:  ✅ Web Server Credentials Update

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

```kusto
let TA_domains = 
PassiveDns
| where domain == 'newdevelopmentupdates.org'
| distinct ip
| lookup PassiveDns on ip
| distinct domain;
Email
| where recipient == "anita_bath@framtidxdevcorp.com"
| where link has_any(TA_domains)
```

17. What is the link attached to that email?

Ans:  ✅ https://greenprojectnews.net/share/modules/files/share/enter

```kusto
let TA_domains = 
PassiveDns
| where domain == 'newdevelopmentupdates.org'
| distinct ip
| lookup PassiveDns on ip
| distinct domain;
Email
| where recipient == "anita_bath@framtidxdevcorp.com"
| where link has_any(TA_domains)
| project link
```

18. When did Anita click on the link? (Paste the full timestamp.

Ans:  ✅ 2024-06-26T15:24:20Z

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

```kusto

OutboundNetworkEvents
| where src_ip == "10.10.0.8"
| where url == "https://greenprojectnews.net/share/modules/files/share/enter"
| project timestamp
```

19. What is the full url showing her doing just that?

Ans: ✅ https://greenprojectnews.net/share/modules/files/share/enter?username=anbath\&password=\*\*\*\*\*\*\*\*\*\*



<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

```kusto
OutboundNetworkEvents
| where src_ip == "10.10.0.8"
| where url startswith 'https://greenprojectnews.net/share/modules/files/share/enter'
| where url has "username"
| project url
```

20. &#x20;Who sent Anita the mail?

Ans:  ✅ alex\_johnson@framtidxdevcorp.com (refer q16 )

21.
