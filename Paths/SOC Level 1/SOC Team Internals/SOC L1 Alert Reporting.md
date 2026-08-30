# SOC L1 Alert Reporting

## Task 1 - Introduction

In the process of alert triaging or even after that there may rise situation in which L1 analysts may require senior support due to cases that may need remediation efforts or immediate action like victim's account blockage etc. These scenarios are covered in this room with the introduction of three new terms, alert reporting, escalation and communication.

### Learning Objectives

- Understand the need for soc alert reporting and escalation
- Learn how to write alert comments or case reports properly
- Explore escalation methods and communication best practices
- Apply the knowledge to triage alerts in a simulated environment

### Prerequisites

- Have a basic understanding of common attacks
- Know responsibilities of SOC L1 Analysts

---

## Task 2 - Alert Funnel

Previously we learned how to classify and triage alerts, but what happens next ? How does the alert triage help stop breaches ? This will be covered here but lets first rewind the path of alerts.

First L1 analysts receive the alerts in a SIEM or EDR or a ticket management platform. Most of the alerts are handled at the L1 level and closed as False Positive but some are sent to L2 for remediation. 

### Alert Reporting

Before closing the alert on our side we may need to document our reasoning and process on the basis of which we are escalating or closing the alert. This may range from a short comment to a proper documented report depending on the team standards.

### Alert Escalation

If the True Positive alert requires more examination or immediate action, then they are escalated to L2 analysts. This is where the alert report comes in since they help the L2 to gain initial context.

### Communication

Communication and coordination with other teams may also be required for e.g, confirming from the IT dept that if they have given some user additional privieges on purpose or asking hr about the new hire.

### Answer the questions below

- What is the process of passing suspicious alerts to an L2 analyst for review?

    `Alert Escalation`

- What is the process of formally describing alert details and findings?

    `Alert Reporting`

---

## Task 3 - Reporting Guide

Writing reports is also an essential skill beside marking an alert true positive or false positive. Reports provide context for L2 analysts and allows them to work efficiently. Also they help L1 analysts understand the alert thoroughly and boost their skills.

### Report Format

Report format usually follow the five W's format:

- **Who** - Which user logs in, runs the command, downloads the file
- **What** - What exact action or event sequence takes place
- **When** - When did the suspicious activity start and end
- **Where** - Which device, IP or website was involved in the alert
- **Why** - Reasoning for final verdict (most imp)

### Answer the questions below

- According to the SOC dashboard, which user email leaked the sensitive document?

    `m.boslan@tryhackme.thm`

- Looking at the new alerts, who is the "sender" of the suspicious, likely phishing email?

    `support@microsoft.com`

- Open the phishing alert, read its details, and try to understand the activity.
Using the Five Ws template, what flag did you receive after writing a good report?
Note: Assign yourself, move the alert to In Progress, and fill in the Analyst Comment.
    
    #### Process:

    First we asign ourself to the alert. Then we review the content and try to reach a verdict. In this case the alert was a true positive as it included urgent action and a suspicious `.rar` extension file disguised as an attached report. 

    We then write our remarks using the five W's approach and clicking save will give us the flag:

    ![Phishing email case](../../../IMAGES/Paths/SOC%20Level%201/SOC%20Team%20Internals/SOC%20L1%20Alert%20Reporting/phishing%20email%20case.png)

---

## Task 4 - Escalation Guide

After we have reviewed an alert, we may need to escalate it to L2 Analysts. There are some common metrics as when an alert may be eligible to be escalated to L2 Analysts:

- The alert is an indicator of a major cyberattack and requires deeper inquiry or DFIR
- Remediation is neede like malware removal, host isolation or password reset
- Communication is required with customers, partners, management or law enforcement agencies
- L1 Analyst doesnt understand the alert fully and needs guidance

### Escalation Steps

To escalate an alert mostly we may need to reassign it to the L2 Analyst on shift and ping them in chat. However, the exact procedure may differ from team to team and may require to file a written request with multiple required fields.

### Answer the questions below

- Who is your current L2 in the SOC dashboard that you can assign (escalate) the alerts to?

    `E.Fleming`

- What flag did you receive after correctly escalating the alert from the previous task to L2?
Note: Set the right assignee and add an intermediate verdict (TP or FP)

    #### Process:

    We assign the L2 Analyst to the alert, mark the verdict and then change the progress to `In Progress`. Clicking Save will show us the flag:

    ![Phishing email escalation to L2](../../../IMAGES/Paths/SOC%20Level%201/SOC%20Team%20Internals/SOC%20L1%20Alert%20Reporting/escalation%20to%20L2%20Phishing%20Email%20case.png)

- Now, investigate the second new alert and provide a detailed alert comment.
What flag did you get after escalating this alert according to the workflow?

    #### Process:

    We first assign the alert to ourself. Then review its content and understand the alert fully. 

    This alert was about domain discovery commands in which the attacker was trying to discover domain admins for Active Directory, so its a valid alert and shows that the attacker has gained access to the system and is now looking to escalate to higher privilege account.

    Therefore, this alert needed escalation. Now we can reassign it to L2 (E.Fleming) and then write a brief comment based on five W's approach and clicking save will give us the flag:

    ![AD Domain Discovery Commands](../../../IMAGES/Paths/SOC%20Level%201/SOC%20Team%20Internals/SOC%20L1%20Alert%20Reporting/escalation%20to%20L2%20Spike%20of%20AD%20Domain%20disocvery%20commands.png)

---

## Task 5 - SOC Communication

The escalation steps seem straight forward but it si not always like it and some unexpected scenarios can take place as well which are discussed briefly below.

### Communication Cases

- **You need to escalate an urgent, critical alert, but L2 is unavailable and does not respond for 30 minutes.**

    Ensure you know where to find emergency contacts. First, try to call L2, then L3, and finally your manager.

- **The alert about Slack/Teams account compromise requires you to validate the login with the affected user.**

    Do not contact the user through the breached chat - use alternative contact methods like a phone call.

- **You receive an overwhelming number of alerts during a short period of time, some of which are critical.**

    Prioritise the alerts according to the workflow, but inform your L2 on shift about the situation.

- **After a few days, you realise that you misclassified the alert and likely missed a malicious action.**

    Immediately reach out to your L2 explaining your concerns. Threat actors can be silent for weeks before impact.

- **You can not complete the alert triage since the SIEM logs are not parsed correctly or are not searchable.**

    Do not skip the alert - investigate what you can and report the issue to your L2 on shift or SOC engineer.

### Answer the questions below

- Should you first try to contact your manager in case of a critical threat (Yea/Nay)?

    `Nay`

- Should you immediately contact your L2 if you think you missed the attack (Yea/Nay)?

    `Yea`

---



