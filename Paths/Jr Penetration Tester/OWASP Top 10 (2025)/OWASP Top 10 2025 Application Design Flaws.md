# OWASP Top 10 2025: Application Design Flaws

## Task 1 - Introduction

This room covers 4 of the OWASP Top 10 2025 categories related to failures in architecture and system design. The categories covered here are: 

- AS02: Security Misconfigurations
- AS03: Software Supply Chain Failures
- AS04: Cryptographic Failures
- AS06: Insecure Design

## Task 2 - AS02: Security Misconfigurations

Security Misconfigurations occur when software, servers or applications are deployed with unsafe defaults, incomplete settings or are exposed rather than kept private. These are not reated to insecure coding but are caused due to misconfigured setups and deployments of applications. These widen the attcak surface and create easy access for attackers.

This matters cause a single exposed service can lead to privilege escalation, expose PIIs or give attackers an access route into the system. This can be in form of exposed admin panels, open storage buckets or misconfigured permissions.

### Example

In 2017, Uber exposed a backup AWS S3 bucket with sensitive user data, including driver and rider information, because the bucket was publicly accessible. Attackers could download data directly without needing credentials. This shows how a deployment mistake can lead to a significant breach.

### Common Patterns

- Default credentials or weak passwords
- Unnecessary exposed services or endpoints
- Misconfigured cloud settings and permissions
- Unrestrictted API access or missing or weak authorisation/authentication
- Detailed error messages exposing stack traces or system details
- Outdated softwares, frameworks or containers with known vulnerabilities
- Exposed AI/ML endpoints without proper access controls 

### Prevention

Harden default configurations and remove unused features and services
Implement strong authentication and least privilege across all systems
Limit network exposure and segment sensistive resources
Keep softwares and services up-to-date 
Decrease the verbosity of error messages and hide system information
Conduct regular audits of cloud configurations and permissions regularly
Secure AI endpoints with proper access controls and monitoring
Integrate configuration reviews and automated security checks in the deployment pipeline

### Challenge

Navigate to `MACHINE_IP:5002`. It appears that the developers left too many traces in their User Management APIs.

### Answer the questions below

- What's the flag?

    First lets test the api endpoint for a normal input and see its response:

    ![user management api](../Images/OWASP%20Top%2010%20(2025)/Application%20Design%20Flaws/user%20api%20test.png)

    The api lists a user's details on supplying an integer input. Lets try by supplying a non integer input to see how it behaves:

    ![user management api](../Images/OWASP%20Top%2010%20(2025)/Application%20Design%20Flaws/flag%201.png)

    So this confirms that the API doesnt handles errors gracefully and reveals too much information.

---

## Task 3 - AS03: Software Supply Chain Failures

Software Supply Chain failures when applications rely on third party components, services librariries or models that are outdated or improperly verified. These weaknesses are not inherent in the code but exist due to a dependency problem. Attackers can exploit these weak links to injet malicious codes, bypass security or steal sensitive data.

Modern applications rely on many third party packages for functionality like APIs and AI models. One compromise dependency can compromise entire systems allowing access to without touching our code.

### Example

In 2021, the SolarWinds Orion compromise showed the danger of supply chain attacks. Attackers inserted malicious code into a trusted update, affecting thousands of organisations that automatically installed it. This wasn’t a bug in SolarWinds’ core logic. It was a flaw in the software update building, verification, and distribution process.

With AI, we can observe this when using unverified third-party models or fine-tuned datasets that can embed hidden behaviours, backdoors, or biased outputs, compromising systems or leaking data.

### Common Patterns

- Using unverified libraries or dependencies
- Authomatically installing updates without verification
- Over reliance on third party AI models without monitoring or auditing
- Insecure build pipelines or CI/CD processes that allow tampering  
- Poor licenses or provenance tracking of components
- Lack of vulnerability monitoring in dependencies after deployment

### Protection

- Verify all third party dependencies before usage
- Monitor and patch dependencies regularly
- Sign, verify and audit softwares and packages 
- Lock down CI/CD and make them tamper resistant
- Track licensing and provenance for all dependencies
- Implement run time monitoring to observe unusual behaviour from dependencies or AI components
- Integrate supply chain threat modelling into the SDLC, including testing, deploying and updating workflows

### Challenge

Navigate to `MACHINE_IP:5003`. The code is outdated and imports an old lib/vulnerable_utils.py component. Can you debug it?

### Answer the questions below

- What's the flag?

    Navigate to the url and see if there is anything interesting there. The page looks like an API documentation page listing two APIs:
    
    ![Data Processing Service Page](../Images/OWASP%20Top%2010%20(2025)/Application%20Design%20Flaws/data%20processing%20service%20page.png)

    Looking at the page source reveals nothing interesting so lets move to the provided code file:

    ![Data Processing Service Page](../Images/OWASP%20Top%2010%20(2025)/Application%20Design%20Flaws/debug%20info%20code.png)

    Here we can see thsi lists down the usage for both the APIs. The health api implementation looks normal and returns a fixed response. 

    However the process api gives us different responses. If there is no data it gives a normal missing data error. But if data is equal to `debug` the api makes a call to `debuginfo()`

    So lets try this out:

    ![Flag 2](../Images/OWASP%20Top%2010%20(2025)/Application%20Design%20Flaws/flag%202.png)

---

## Task 4 - AS04: Cryptographic Failures

Cryptographic failures occur either due to lack of encryption or due to improper implementation. This may be due to use of weak algorithms, hard-coded keys, poor key handling or unencrpted sensitive data. These flaws can lead to attackers gaining access to information that was supposed to be private.

Web applications use cryptography for protectig network traffic, securing stored data, verifying identities and safeguarding secrets. When these controls fail, sensitive data such as passwords, tokens, or personal information can be exposed, leading to account takeovers or full-scale breaches.

### Common Patterns

- Using weak algorithms like MD5, SHA-1 or ECB mode 
- Hardcoded secrets or configurations
- Poor key rotation or management practices
- Lack of encryption for sensitive data at rest or in transit
- Self signed or invalid TLS certificates
- Using AI/ML systems without proper secret handling for model parameters or sensitive inputs

### Prevention

- Use strong modern algorithms like AES-GCM, ChaCha20-Poly1305, or enforce TLS 1.3 with valid certificates
- Use secure key management services like Azure Key Vault, AWS KMS or HashiCorp Vault
- Rotate keys after defined crypto periods
- Document and enforce policies and standards operating procedures for key lifecycle management
- Ensure AI models never expose unencrpted secrets or sensitive data

### Challenge

Navigate to `MACHINE_IP:5004`. Can you find the key to decrypt the file?

### Answer the questions below

- What's the flag?

    Navigating to the url gives us the follwoing simple page:

    ![Secure Document Viewer Website](../Images/OWASP%20Top%2010%20(2025)/Application%20Design%20Flaws/secure%20document%20viewer%20website.png)

    The page claims that only confidential persons can decrypt and view the document and also states that decryption feature is currently unavailable. 

    So lets inspect the page source to see if we could find any interesting thing:

    ![Secure Document Viewer Website Code](../Images/OWASP%20Top%2010%20(2025)/Application%20Design%20Flaws/secure%20document%20viewer%20inspect.png)

    Here we can see that there is a script file. Appending the script path to the base url gives us the following js code:

    ![Secure Document Viewer Website](../Images/OWASP%20Top%2010%20(2025)/Application%20Design%20Flaws/decrypt.js.png)

    From here we can see that the application is using a weak cryptographic algorithm ECB mode and also reveals the key size of 128 bits and the secret key as well. So we can crack the encryption of the document through these information. 

    I seacrched for ECB mode decryption websites and found the following website: https://www.devglan.com/online-tools/aes-encryption-decryption. Entering the algorithm details and the encrypted text from the webpage gave us the flag:

    ![Secure Document Viewer Website](../Images/OWASP%20Top%2010%20(2025)/Application%20Design%20Flaws/flag%203.png)

---

## Task 5 - AS06: Insecure Design

Insecure Design happens when the project architecture or logic is flawed from the begining.  These flaws stem from skipped threat modelling, no design requirements or reviews, or accidental errors.

Moreover, with the introduction of AI assistants, AI systems exacerbate insecure design. Developers often assume that models are safe, correct, or predictable, or that the code they produce is flaw-free. 

Moreover patching this is not possible cause the problem lies in the assumptions and logic not in the code itself.

### Example

A good example is Clubhouse. Its early design assumed users would only interact through the mobile app, but the backend API had no proper authentication. Anyone could query user data, room info, and even private conversations directly. When researchers tested it, "he entire "private c "nversation" premise fell apart.

### Common Insecure Designs 

- Weak business logic controls, like recovery or approval flows
- Flawed assumptions about user or model behaviour
- AI components with unchecked authority or access
- Missing guardrails for LLMs and automation agents
- Test or debug bypasses left in production
- No consistent abuse-case review or AI threat modelling

### How To Design Securely

- Treat every model as untrusted until proven otherwise.
- Validate and filter all model inputs and outputs to ensure accuracy and integrity.
- Separate system prompts from user content.
- Keep sensitive data out of prompts unless absolutely needed and protect it with strict controls.
- Require human review for high-risk AI actions.
- Log model provenance, monitor behaviour, and apply differential privacy for sensitive data.
- Include AI-specific threat modelling for prompt attacks, inference risks, agent misuse, and supply chain compromise throughout the design process.
- Build threat modelling into every stage of development, not just at the start.
- Define clear security requirements for each feature before implementation.
- Apply the principle of least privilege across users, APIs, and services.
- Ensure proper authentication, authorisation, and session management across the system.
- Keep dependencies, third-party components, and supply chain sources verified and up to date.
- Continuously monitor and test the system for logic flaws, abuse paths, and emergent risks as new features or AI components are added.

### Challenge

Navigate to `MACHINE_IP:5005`. Have they assumed that only mobile devices can access it?

### Answer the questions below

- What's the flag?

    Lets start by navigating first to the given url. The page contains the follwowing:
    
    ![Secure Chat Website](../Images/OWASP%20Top%2010%20(2025)/Application%20Design%20Flaws/secure%20chat%20website.png)

    So lets see if they have an `/api` endpoint:

    ![api endpoint](../Images/OWASP%20Top%2010%20(2025)/Application%20Design%20Flaws/api%20endpoint.png)

    If we look back from the Clubhouse example the text says: ` Anyone could query user data, room info, and even private conversations directly.`

    So maybe there is some `/users` endpoint ? 

    ![users endpoint](../Images/OWASP%20Top%2010%20(2025)/Application%20Design%20Flaws/users%20api%20endpoint.png)

    So we get some user details from this endpoint but our main goal iss to find the messages so most probably the endpoint could be `/api/messages` or `/api/users/messages` ?

    ![api endpoint](../Images/OWASP%20Top%2010%20(2025)/Application%20Design%20Flaws/messages%20api%20endpoint.png)

    What if we append a user at the `/messages` endpoint ?

    Let's try by appending the `admin` user discovered earlier to see if it gives admin's message history or any chat:

    ![api endpoint](../Images/OWASP%20Top%2010%20(2025)/Application%20Design%20Flaws/flag%204.png) 

    So the real endpoint was the `/api/messages/<user>` and it really breaks the assumption that the messages are private and app is only mobile accessible.

    Full detailed explanation can be found here: https://youtu.be/LYthpju91L8?si=QMtavPdiWEinS3_k

---

## Task 6 - Conclusion

All of these four mentioned flaws arise due to weak foundations. Strong security starts with clear requirements, good design, authorised dependencies, controlled configurations and good cryptographic choices.

---