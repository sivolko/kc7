---
icon: '2'
---

# Welcome to Valdoria !

1. What is the Newspaper Printer's Name?

&#x20;      Ans: Clark Kent&#x20;

2.

<details>

<summary>Next, you talk with <code>Clark Kent</code>. He seems very distressed about the whole situation. 😓 He tells you he simply printed the article that was emailed to him, as he always does.</summary>

now let's write KQL where role == "Editorial Intern"

```aql
// Some code

Employees 
| where role == "Editorial Intern" 
```

result will be&#x20;

![](<../.gitbook/assets/image (22).png>)

Ans : &#x20;

```json
Ronnie McLovin
```

</details>

3.  **When was the Editorial Intern hired at The Valdorian Times?**

    \
    looking at the above KQL result there is column name hire\_date.&#x20;

Ans : &#x20;

```json
2024-01-02T08:00:00Z
```

4. Q  **How many total emails has Clark Kent received?**

**Ans :**  ![](<../.gitbook/assets/image (7).png>)

```kusto
// KQL

Email 
| where recipient == "clark_kent@valdoriantimes.news"
| count
```

5. What was the subject line of this email ?&#x20;

Ans :  Let's modify previous Kql with date and timestamp&#x20;

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

```kusto
// KQL

Email 
| where recipient == "clark_kent@valdoriantimes.news"
| where timestamp between  (datetime(2024-01-31T00:00:00Z) .. datetime(2024-02-01T00:00:00Z))

```

URGENT: Final OpEd Draft Edits (Please publish the following article in tomorrow's paper))

6. **Enter the sender's email address.**

Ans : In previous KQL there is sender column that's answer

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

7. **What was the name of the .docx file that was sent in this email?**

Ans :-  expand the carat located on the left side of the result&#x20;

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

8. **Do you think this needs further investigation (yes/no)? Choose wisely 😉**

Ans :- yes
