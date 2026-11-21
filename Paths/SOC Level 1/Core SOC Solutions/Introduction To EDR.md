# Introduction To EDR

## Task 1 - Introduction

Endpoint Detecton and Response is security solution that helps detect and respond to threats at the endpoint level. It is a widely adopted solution across many organizations for protecting endpoints.

### Learning Objectives

- Understand the basics of EDR and how it works
- Differentiate EDR from traditional Antivirus solutions
- Examine the architecture of an EDR solution
- Analyze the types of telemetry it collects from endpoints
- Understand the detection and response capabilities of an EDR
- Investigate a realistic alert in the EDR

---

## Task 2 - What is an EDR ?

The increase in the use of digital devices has also increased the number of attacks. Most of these attacks can catered on the network level but since the adoption of remote devices that operate out of the organizations network perimeter the endpoint lvel detection has gained quite significance.

Below are some of the EDR solutions in the market:

- CrowdStrike Falcon
- Symantec EDR
- SentinelOne EDR
- Microsoft Defender for Endpoint

### Features Of EDR

Regardless of the EDR solution in use all EDRs share a common architecture and baseline features. There are three main pillars of an EDR:

- Visibility
- Detection
- Response

#### Visibility

A good analysis depends on the amount of the visbility of the ativity that the defenders get. The EDRs provide extensive amount of visibility and collect detail data from endpoints like process modifications, file/folder modifications, user actions etc.  The analyst can see the whole process tree with a complete activity timeline of the sequence of actions. The analyst can also access the historical data of any endpoint for threat hunting or any other purpose.

![Visibility](../../../IMAGES/Paths/SOC%20Level%201/Core%20SOC%20Solutions/Introduction%20To%20EDR/Visibility.png)

#### Detection

The detection feature of an EDR is also robust as it incorporates signature based detection as well as beahviour based analysis. Leveraging modern machine learning capabilities it can detect deviations from normal behavior and flag it. It also allows feeding of custom IOCs for threat detections.

![Detections](../../../IMAGES/Paths/SOC%20Level%201/Core%20SOC%20Solutions/Introduction%20To%20EDR/Detections.png)

#### Response

EDR also provides analysts with the capability to take control over the target device and perform actions. These actions can be taken for any device within in the EDR console. You can isolate an infected host, qurantine threats and connect remotely and execute actions.

![Response](../../../IMAGES/Paths/SOC%20Level%201/Core%20SOC%20Solutions/Introduction%20To%20EDR/Response.png)

With advanced visibility, detection, and response, EDR becomes a very powerful tool. However, it's also important to remember that an EDR is a host-only security solution and does not detect network-level threats.

### Answer the questions below

- Which feature of EDR provides a complete context for all the detections?

    `Visibility`

- Which process spawned sc.exe?

    `cmd.exe`

---

## Task 3 - Beyond the Antivirus

### Why do we need an EDR when we already have an Antivirus (AV) on the endpoints?

This is a common question that may come in our mind that why we have to choose EDR when we can have Antivirus installed. The answer is as simple as the question itself and it is due to the fact the Antiviruese often detect threats through a mechanism of signature base detection and thsi si why they may miss threats that are advanced. Here the EDR will come in handy as it detects the full behaviour of the endpoint.

Just like on an airport an immigration officer may check persons against a list of known criminals and if matched they would catch that person. But if a person who has no previous criminal record or whose name is not included passes by the immigration officers may ignore him. While on the other hand there also security officers in an airport whose work is to monitor the overall situations and actions of individuals within the airports's premise and even if a person evades the immigration checklist he can get noticed by his unusual behaviours by the security officer much like an EDR.

Let's examine an advanced malicious activity step by step on an endpoint and compare the response of an AV and EDR at each stage.

### Scenario Breakdown

- **Step #1:** A user receives a phishing email with a Word document embedded with a malicious macro (VBA script)
- **Step #2:** The user downloads the document and opens it
- **Step #3:** The malicious macro is silently executed, and it spawns PowerShell
- **Step #4:** The malicious macro runs an obfuscated PowerShell command to download a sophisticated second-stage payload
- **Step #5:** The payload is injected into a legitimate svchost.exe
- **Step #6:** The attacker gains remote access to the system

![AV scenario](../../../IMAGES/Paths/SOC%20Level%201/Core%20SOC%20Solutions/Introduction%20To%20EDR/AV%20scenario.png)

| Attack Steps | AV's Response | EDR's Response |
|--------------|---------------|----------------|
| Step #1 | Does nothing if the downloaded file has no previous signature in the database | Logs the file download activity and monitors it |
| Step #2 | Does nothing upon the opening of the document since winword.exe is a legitimate utility | Records the execution of winword.exe and keeps monitoring |
| Step #3 | Does nothing if the executed macro has no previous signature | Detects and flags the macro execution due to the unusual parent-child relationship of winword.exe and PowerShell.exe processes |
| Step #4 | Typically, AVs will not detect obfuscated PowerShell scripts | Flags the obfuscated script execution |
| Step #5 | Will not flag malicious injection into svchost.exe since it does not monitor the memory injections | Detects Process Injection in svchost.exe |
| Step #6 | Lacks Network Level Visibility | Flags the unexpected behaviour of svchost.exe, making an outbound connection |
| Final Action | May be marked as clean | Generates an alert with the full attack chain and enables the analyst to take actions from within the EDR |

> Note: Some modern AVs may have adnaced detection capabilities but EDR will have an edge even then cuz it provides detection as well as response facilities for an endpoint 

### Answer the questions below

- In the given analogy, what presents an AV?

    `immigration check`

- Which legitimate process was hijacked by the attacker in the scenario?

    `svchost.exe`

- Which security solution might mark this activity as clean?

    `Antivirus`

---

## Task 4 - How an EDR Works ?

In order to understand how an EDR works we have to understand some basic concepts.

### Agents

We can integrate multiple endpoints with an EDR and manage them through a centeralized console. To monitor the activities of these endpoints we deploy agents on the endpoints. These agents act as the eyes and ears of the EDR and detect all activites on the endpoint and send them to the EDR console.  The EDR agents can do some basic signature-based and behavior-based detections by themselves and send them to the EDR console, which triggers alerts.

### EDR Console

All the data sent by agents to the EDR console gets correlated and analyzed through complex machine learning algorithms. The threat intelligence gets matched with the collected data. The EDR acts like a brain connecting all the dots and these dots connect to form a detection (also called an alert).

The following screenshot shows the dashboard of an EDR console. All the data from the endpoint agents is coming into this console, and the detections are happening here. This dashboard gives a holistic view of the current status of detections in all the endpoints. 

![Dashboard](../../../IMAGES/Paths/SOC%20Level%201/Core%20SOC%20Solutions/Introduction%20To%20EDR/dashboard.png)

### What happens after Detection?

Once an alert comes, the responsibilty of SOC Analysts begins. The EDR automatically priortizes alerts based on severity. A SOC Analyst can select an alert and then determine if its a true positive or false positive based on details. If true positive, the analyst can move forward with it to take relevant actions through the console.

### EDR with Other Tools

Although, a single EDR provide a good enough insight to manage threats it is important to note that mostly EDR works with other tools as well to form a much broader security solution. Within a network, we will see Firewalls, DLPs, Email Security Gateways, IAMs, EDRs, and other security solutions protecting the different components of the network. To minimize the effort and maximize the efficiency, all these security solutions are integrated with a SIEM solution that becomes the central point of investigation for the analysts. 

### Answer the questions below

- Which component of the EDR is responsible for collecting telemetry from the endpoints?

    `Agent`

- An EDR agent is also known as a?

    `sensor`

---

## Task 5 - EDR Telemetry

### What is Telemetry

Telemetry is simply the data collected from the endpoint by the EDR Agents. This data contains everything needed for detection and investigation.

### Collected Telemetry

Usually many activities are happening at the endpoints and it is difficult to distinguish normal and malicious activity. Therefore, the more data is there the more clearer and easier it gets to detect anomalies. EDR collects detailed telemetry from the endpoints some of which are described below:

- #### Process Executions and Terminations

    It keeps of all processes which are running or idle, which helps in detecting suspicious child parent process relationships, suspicious executables initiating a process, malware payloads etc.

- #### Network Connections

    All network connection of the endpoints are monitored that helps in detecting communication with C2 channels, data exfiltration attempts or lateral movement within the network.

- #### Command Line Activity

    It captures all command executed on the endpoints in CMD, Powershell, which helps to identify malicious command execution and obfuscated powershell scripts executions.

- #### Files and Folder Modifications

