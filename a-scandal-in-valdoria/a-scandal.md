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

6.

