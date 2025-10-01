# Use Cases

## Arun Use Case: Secure Web Connection
![Navigate to URL Use Case](docs/Secure%20web%20connection.png)
=======
### Security Requirements:
- Strict Certificate Validation → prevents Rogue Certificate Attack
- Mutual TLS Authentication → strengthens identity checks against MITM Attack
- Encrypt Authentication Data → protects confidentiality from MITM Attack
- DNSSEC Validation → prevents DNS Spoof Attacks
- HSTS / HTTPS-Only Enforcement → ensures connections are always encrypted
---

### Reflection:
The Secure Web Connection misuse case analysis emphasized the importance of protections such as strict certificate validation, DNSSEC, HSTS, and mutual TLS in defending against MITM, rogue certificate, and DNS spoofing attacks. Mapping these threats directly to countermeasures clarified how individual mechanisms contribute to overall browser security. Reviewing Ladybird’s current state also highlighted that while the architecture supports strong isolation and TLS handling, several of these requirements are not yet fully documented or implemented, leaving a gap between expected and actual security capabilities.

### AI Prompt
The following prompt was provided to ChatGPT to help iterate the misuse case development:

>
>You are an expert software security requirement engineer.
>
>Your job is to suggest misuse cases for a particular description of a use case diagram.
>
>Misuse cases need to be introduced in stages as back-and-forth analysis by introducing security countermeasures in response to a misuse case.
>
>Use this text description of a use case for the analysis.
>
>"""
>
>An Actor is associated with the "Secure Web Connection" use case.
>The "Secure Web Connection" use case has dependencies with "Mutual TLS Authentication," "Encrypt Authentication Data," and "DNSSEC Validation" use cases.
>Identified misuse cases include "MITM Attack," "Rogue Certificate Attack," and "DNS Spoof Attack." Each misuse case should map to a corresponding countermeasure (e.g., Strict Certificate Validation, HSTS/HTTPS-Only Enforcement, DNSSEC/DoH).
>
>"""
>
>

---


## Logan Use Case: File Downloading
![Navigate to URL Use Case](docs/UseCase_File_Download.png)
=======
### Security Requirements:
- Display Full Filename → mitigates File Name/Extension Spoofing
- Validate Download Source → prevents Download Redirect/Drive-by Download
- Download Confirmation Popup → prevents Drive-by Download
- Scan File for Virus → prevents Malware Executed on Browser User's Machine
- Sandboxing & Supported Schemes → mitigates automatic downloads and redirects

### Implemented security features:
Display full filename on download (UI/Qt/Application.cpp), validate dowload source (link trustworthiness check, Meta/gn/build/download_file.py), download confirmation popup (UI/Qt/Application.cpp) are all implemented within the browser. Could use a virus scanner but the existing security implementations are strong regardless. There is a file integrity check based on sha256 signature and also a sandboxing environment allowed for downloads.

### Reflection:
I learned a lot about how versatile the browser is and that there are likely quite a few features in the mainstream browsers that promote security that I am unaware of. The sandboxing environment was very interesting to learn about in particular as I've learned in my professional work that it can have a lot of really interesting usecases. The diagramming was particularly difficult to learn for me because visually explaining how a technology interaction works is not very straightforward buit was a good skill to practice. 


---

## Alex Use Case: Password Input
![Password Input Misuse Case](docs/Password_Input_Misuse_Case.drawio.png)
=======
### Security Requirements:
 - Hide the text inside Input elements of type `Password` by default.
 - Support Content Security Policy headers inside HTTP responses from sites.
 - Support the `script-src` directive (and similar derived directives) to specify specific allowed file hashes.

### Implemented security features:
Supports Content Security Policy, supports script-src, script-src-elem, and script-src-attr. Supports using nonce values for use with dynamic, inline scripts. Ladybird seems to have the security requirements derived from this diagram implemented fully. When inspecting the code, I found comments specifying the W3C standard number for whatever was being implemented in that block, so it seems to be a thorough implementation that goes through the standard for all of these items.

I could not find this behavior in the codebase, but after compiling and running the browser, I found that it does mask the text in input elements of type `password`, protecting from shoulder surfing. 

---

### Reflection:
I found this assignment to be useful for getting me in the right mindset for useful security analysis. I started this class thinking "buffer overflows" and "modifying memory" - rarer and relatively difficult types of attacks. Trying to come up with good use/misuse cases helped me to remember the small security features we usually don't think of - in this case, it was obscuring password inputs. Starting from that foundation, I was more easily able to build out a diagram of more realistic threats.


---

## Jason Use Case: Navigate to Url
![Navigate to URL Use Case](docs/UseCase_Navigate_to_URL.svg)
=======
### Security Requirements:
- Certificate Validation and Revocation
  - Strict Certificate Validation
  - Online Certificate Status Protocol (OCSP) Stapling
- Content Security Policy (CSP) Enforcement
  - Prevent Inline Script Execution
  - Restrict Script Origins
- Block Automatic Downloads

---
### Reflection: 
I reviewed the Ladybird browser GitHub project documentation and discussion in the project’s Discord channel to determine the current state of security-related information within the project. The existing project documentation does not feature a dedicated thread or section specifically focused on security topics. This indicates that currently security is not addressed as a standalone subject within the available documentation. Much of the Ladybird browser documentation is aimed at attracting and assisting new contributors. The materials primarily cover coding style guidelines and detailed build instructions, rather than addressing security concerns or practices.

---

### AI Prompt Development
The following prompt was provided to ChatGPT to help iterate the misuse case development:

>You are an expert software security requirement engineer. Your job is to critique and improve on the existing misuse cases and to suggest additional misuse cases for a particular description of a use case diagram. 
>
>Mis use cases need to be introduced in stages as back and forth analysis by introducing security countermeasures in response to a mis use case. 
>
>We are building a misuse case diagram for an open sourced web browser. Use this text description of a use case for the analysis. 
>
>""" 
>
>A User is associated with the "Navigate to URL" use case. The "Navigate to URL" use case has a dependency to <<include>> "Transmit User Data" , "Render Website" and "Download Website Data" use cases. 
>
>""" 
>
>Three misuse cases have already been identified "MITM Attack", "XSS Attack", and "Malicious Download" all three misuse cases results in "Loss of Confidentiality/Integrity/Availability" 
One security countermeasure has been identified for each of the three misuse cases. "Validate TLS" mitigates "MITM Attack". "Client-Side input validation" mitigates "XSS Attack" and "Block Automatic Downloads" mitigates "Malicious Download" 
>
>Goal number 1: Critique the existing use cases and misuse cases. Am I on the right path? 
>
>Goal number 2: Iterate additional use case and misuse cases to expand on the existing use cases

---

## Godwin Use Case: Blocking Pop Ups
![Navigate to URL Use Case](docs/PopUp_Blocking.jpg)
### Security Requirements:
- User-gesture–gated pop-ups → prevents unsolicited pop-ups that can lead to phishing pop-ups and malicious redirects.
- Block cross-origin popup redirection by default → prevents malicious redirect.
- Disallow downloads from popup context → prevents drive-by downloads
---

### Reflection:
Reviewing the Pop up misue case, the mitigation/defense chain is straight forward within ladybird. The permission option/prompt is the central control for this use case and extends to three mitgation being disallowing downloads, blocking cross-origin redirection, and restricting window.open(). This exercise exposed to me to some of the basic functionality that browsers provide have much deeper security implementations and extensions.


### AI Prompt Development
The following prompt was provided to ChatGPT to help iterate the misuse case development:

>You are an expert software security requirement engineer. Your job is to critique and improve on the existing misuse cases and to suggest additional misuse cases for a particular description of a use case diagram. 
>
>Mis use cases need to be introduced in stages as back and forth analysis by introducing security countermeasures in response to a mis use case. 
>
>We are building a misuse case diagram for an open sourced web browser. Use this text description of a use case for the analysis. 
>
>""" 
>
>A User is associated with the "Block Pop Ups" use case. The "Block Pop Ups". This use case will be extended from a central control of a permission prompt.
>
>""" 
>
>
>Goal number 1: Iterate additional use case and misuse cases to expand on the existing use cases


## Part 2:

## OSS Analysis Takeaways:
The Ladybird project documentation reflects a commitment to modern security practices, and based on some of their monthly postings and general documentation, it's clear that there are steps being taken to further improve browser security. Through our individual research, our team identified some key shortcomings that we could use our skills to help mitigate. For starters, there is a lack of detailed guidance on configuring security-related options, such as enabling HTTPS-only mode or handling mixed content, which are crucial for protecting users from common web threats. Download validation features could be enhanced with more warnings of malicious downloads and better logging of download history, as well as better documentation on the uses of sandboxing for isolating downloaded content. Password and script handling have a strong start with masking passwords and enforcing content security policy, but the documentation lacks in descriptions for configuration and limitation. Popup blocking suffers a similar fate, where there are basic protocols that midigate browser pop up attacks, but lack of documentation of what that entails. The clear shortcoming of this project actually is the documentation of what security features exist. This is certainly somewhere we can contribute, but as listed here we have some technical pieces that would also be beneficial to the browser's security overall. Overall, we see in this project a weakness in that even with good intentions for security, there is a lack of proper documentation that makes meaningful contributions for individuals unfamiliar with the project rather difficult, but we see a path forward for how we can improve the browser as a whole. 


## Project board:
[Link](https://github.com/users/chauler/projects/3)
