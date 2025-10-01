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
## Part 2:
### Summary:
Ladybird’s documentation highlights modern TLS and ongoing certificate validation work, but it lacks depth on security-related configuration and installation. Important protections like DNSSEC validation, HSTS/HTTPS-Only enforcement, certificate transparency, and mixed content handling are either incomplete or undocumented. Strengthening security documentation would give contributors and users better visibility into the project’s security posture.

## Key Points:
- Modern TLS and HTTPS supported
- Certificate validation improvements in progress
- Missing: DNSSEC validation, certificate transparency, revocation checks
- Unclear: HSTS/HTTPS-Only mode, mixed content handling
- Improvement: Add explicit TLS feature docs, security roadmap to SECURITY.md

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

Reflection: I learned a lot about how versatile the browser is and that there are likely quite a few features in the mainstream browsers that promote security that I am unaware of. The sandboxing environment was very interesting to learn about in particular as I've learned in my professional work that it can have a lot of really interesting usecases. The diagramming was particularly difficult to learn for me because visually explaining how a technology interaction works is not very straightforward buit was a good skill to practice. 

## Part 2:
### Summary:
It looks to me like there is potential to do some more work as far as validating downloads by providing warnings when a file comes from an untrusted source, and some kind of log that saves when a download was made and what was downloaded would also be beneficial. Antivirus scanning of files could also be done but I think that might be more advanced than we want to work. 

## Alex Use Case: Password Input
![Password Input Misuse Case](docs/Password_Input_Misuse_Case.drawio.png)
Security requirements:
 - Hide the text inside Input elements of type `Password` by default.
 - Support Content Security Policy headers inside HTTP responses from sites.
 - Support the `script-src` directive (and similar derived directives) to specify specific allowed file hashes.

Implemented security features:
Supports Content Security Policy, supports script-src, script-src-elem, and script-src-attr. Supports using nonce values for use with dynamic, inline scripts. Ladybird seems to have the security requirements derived from this diagram implemented fully. When inspecting the code, I found comments specifying the W3C standard number for whatever was being implemented in that block, so it seems to be a thorough implementation that goes through the standard for all of these items.

I am investigating whether password inputs are obscured. I did not find any obvious references to this behavior within the HTMLInputElement.cpp file, but I will build Ladybird locally and experiment myself.

---

## Reflection:

Alex's reflection:
I found this assignment to be useful for getting me in the right mindset for useful security analysis. I started this class thinking "buffer overflows" and "modifying memory" - rarer and relatively difficult types of attacks. Trying to come up with good use/misuse cases helped me to remember the small security features we usually don't think of - in this case, it was obscuring password inputs. Starting from that foundation, I was more easily able to build out a diagram of more realistic threats.

---

## Part 2:
Security is discussed at a high level [here](https://github.com/LadybirdBrowser/ladybird/security). It addresses the project's mindset regarding security during development and the disclosure and handling of security vulnerabilities. The document points to security being a serious consideration, but not a top priority, when developing new features. This is a mindset that is due to change at some point, though, as the reasoning given for it is the project's current stage of very early development. This document also seems to be out of date. For example, there is a list of items deemed "out of scope"; this list includes "Content Security Policy", though that has been implemented. This was likely moved to "in-scope" when the browser started targeting web standards tests that almost certainly include Content Security Policy testing.

This is not specifically security-related communications, but I also found a monthly update for the project that often covers security improvements and the security implications of implemented features. One example slide from the July update:
<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/a5fc61a9-b486-49fc-a42b-eea0b0f855ee" />


---

## Jason Use Case: Navigate to Url
![Navigate to URL Use Case](docs/UseCase_Navigate_to_URL.svg)
=======
### Navigate to URL Security Requirements
Security Requirements derived from the Navigate to URL use case and misuse cases are:
- Certificate Validation and Revocation
  - Strict Certificate Validation
  - Online Certificate Status Protocol (OCSP) Stapling
- Content Security Policy (CSP) Enforcement
  - Prevent Inline Script Execution
  - Restrict Script Origins
- Block Automatic Downloads

---
## Reflection: 
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
## Summary:
Ladybird’s documentation included many security features, though blocking pop ups is not a main focus it is still an option provided to users that has many implied security extensions. There is a need for more documentation here and also testing of what exactly ladybird controls in terms of blocking pop ups and the criticality it deeems on the security within pop up blocking. Overall, there is basic mitigation protocols but not a robust system dedicated to popular attack within browser pop ups. 
=======

---

## Project board:
[Link](https://github.com/users/chauler/projects/3)
