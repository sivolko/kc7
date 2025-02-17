---
icon: '3'
---

# Plenty of Phish

1. **What is Sonia's job role?**

**Ans**  Senior Editor

```kusto
// KQL

Employees
| where name == "Sonia Gose"
| project  role

```

2. **What email address was used to send this email?**

Ans :- It's already written in image  newspaper\_jobs@gmail.com

3. **When was the email sent to Sonia Gose?** Enter the exact timestamp from the logs.

Ans&#x20;

&#x20;![](<../.gitbook/assets/image (11) (1).png>)

```kusto
// KQL

Email
| where sender =="newspaper_jobs@gmail.com" and recipient has  "Sonia"
| project timestamp
```

4. What URL was included in the email?

Ans&#x20;

<figure><img src="../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

```kusto
// KQL

Email
| where sender =="newspaper_jobs@gmail.com" and recipient has "Sonia"
| project link

```

5. What is Sonia Gose's IP address ?

Ans&#x20;

<figure><img src="../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>

```kusto
// KQL

Employees
| where name == "Sonia Gose"
| project  ip_addr

```

6. Did Sonia click on this link?\
   \
   **If so, enter the timestamp when she clicked the link. If not, type "no".**

Ans  let's check all the url clicked by sonia since we know her ip adrs and url visited it's easy to fetch now&#x20;

**2024-01-05T10:23:17Z**

```kusto
// KQL 

OutboundNetworkEvents
| where src_ip == "10.10.0.3"  and url contains "https://promotionrecruit.com/published/Valdorian_Times_Editorial_Offer_Letter.docx"
| project timestamp

```

<figure><img src="../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

7. What was the name of the docx file in the link that sonia clicked ?

Ans  Valdorian\_Times\_Editorial\_Offer\_Letter.docx

8. **What is Sonia Gose's hostname?**

Ans  UL0M-MACHINE

```kusto
Employees
| where name has "Sonia"
| project hostname
```

9. **When did the downloaded docx file first show up on Sonia's machine?**

Ans  Let's find all file creation Activities&#x20;

```kusto
// KQL    

FileCreationEvents
| where hostname == "UL0M-MACHINE" and filename == "Valdorian_Times_Editorial_Offer_Letter.docx"
| project timestamp
```

<figure><img src="../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

10. **What was the full path of the docx file that was downloaded to Sonia's machine?**

Ans  Just replace timestamp with path&#x20;

C:\Users\sogose\Downloads\Valdorian\_Times\_Editorial\_Offer\_Letter.docx

```kusto
// KQL

FileCreationEvents
| where hostname == "UL0M-MACHINE" and filename == "Valdorian_Times_Editorial_Offer_Letter.docx"
| project path


```

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

11. **What is the sha256 hash of the file that Sonia downloaded?**



Ans  just modify the previous KQL and project it by sha256

60b854332e393a6a2f0015383969c3ac705126a6b7829b762057a3994967a61f

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

```kusto
// KQL

FileCreationEvents
| where hostname == "UL0M-MACHINE" and filename == "Valdorian_Times_Editorial_Offer_Letter.docx"
| project sha256

```

12. **What is the name of the file (.ps1) that was written to disk immediately after the docx was downloaded?**

**Ans** &#x20;

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

```kusto
// KQL

FileCreationEvents
| where hostname == "UL0M-MACHINE"
| where timestamp > datetime(2024-01-05 10:24:04.000) // timestamp after the event
| where filename endswith ".ps1"
| project filename
```

13. **When was this new file created?**



Ans  Just modified previous KQL add Timestamp &#x20;

```json
2024-01-05T10:24:32Z
```

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

```kusto
// KQL

FileCreationEvents
| where hostname == "UL0M-MACHINE"
| where timestamp > datetime(2024-01-05 10:24:04.000) // timestamp after the event
| where filename endswith ".ps1"
| project filename, timestamp
```

14. **Let's do some research! What type of file is this?**

Ans : Powershell

15. **What does the attacker say to "let you know they are here"?**

Ans  lol ur bout 2 get pwnd...;lol ur bout 2 get pwnd

16. **According to the PowerShell script, what might be the hacker's favorite color?**

Ans  Green&#x20;

17. **The purpose of the script is to invoke \_\_\_\_ and uncover da truth**

Ans Plink

18. **How many Process Events are there related to this PowerShell script on Sonia's machine?**

**Ans  3**

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

```kusto
// KQL

ProcessEvents
| where hostname == "UL0M-MACHINE"
| where process_commandline has "hacktivist_manifesto.ps1"
| count
```

19. **What is the full command used to create the scheduled task?**

**Ans**  schtasks /create /sc hourly /mo 5 /tn "Hacktivist Manifesto" /tr "powershell.exe -ExecutionPolicy Bypass -File C:\ProgramData\hacktivist\_manifesto.ps1"

<figure><img src="../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

```kusto
// KQL

ProcessEvents
| where hostname == "UL0M-MACHINE"
| where process_commandline has "hacktivist_manifesto.ps1"
| project process_commandline
```

20. What ExecutionPolicy is set in the command?

Ans  Bypass

* **schtasks**: Command-line tool to manage scheduled tasks.
* **/create**: Create a new task.
* **/sc hourly**: Schedule type: hourly.
* **/mo 5**: Modify schedule: every 5 hours.
* **/tn "Hacktivist Manifesto"**: Task name.
* **/tr "powershell.exe -ExecutionPolicy Bypass -File C:\ProgramData\hacktivist\_manifesto.ps1"**: Action to run the PowerShell script with execution policy bypassed.

21. **What IP address is used when plink is executed?**

Ans  136.130.190.181

<figure><img src="../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

```kusto
// KQL

ProcessEvents
| where hostname == "UL0M-MACHINE"
| where process_commandline has "plink.exe"
| project process_commandline
```

22. &#x20;**What username did the attacker use when connecting via plink?**

Ans from previous KQL  it's there &#x20;

<figure><img src="../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

```json
$had0w
```

23. What password did the attacker use when connecting via plink?

Ans  same from previous KQL&#x20;

```json
thruthW!llS3tUfree
```

24. **What six-letter command did the attackers run to figure out which user they are logged on as on the computer?**

Ans : we can easily guess i.e "whoami"

**but if you need kql , it would be something like**&#x20;

```kusto
// KQL

ProcessEvents
| where hostname == "UL0M-MACHINE"
| where process_commandline has "whoami"
| project timestamp, process_commandline
```

25. **How many discovery commands did the attackers run on this machine?**

**Ans This is little tricky , here we need to take reference of MITRE attack framework T1018 and do keep hit & Trial**&#x20;

**5** &#x20;

* **whoami**: Displays the current username.
* **hostname**: Shows the name of the computer.
* **ipconfig**: Displays network configuration details.
* **netstat**: Shows network connections, routing tables, and interface statistics.
* **systeminfo**: Provides detailed information about the system, including OS version, hardware, and network configuration.

```kusto
// KQL


ProcessEvents
| where hostname == "UL0M-MACHINE"
| where timestamp >= datetime(2024-01-06 02:39:35)
| where process_commandline has_any ("whoami", "hostname", "ipconfig", "netstat", "systeminfo", "tasklist", "net user", "net view", "net localgroup", "arp", "route", "nbtstat")
| project timestamp, process_commandline
```

26. **Do you think we can safely stop our investigation here? (yes/no)**



Ans : No since , system sanitise is pending&#x20;
