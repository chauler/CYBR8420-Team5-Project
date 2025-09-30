# Use Cases

![Navigate to URL Use Case](docs/Secure%20Site%20loading%20final.png)

![Navigate to URL Use Case](docs/UseCase_File_Download.png)

![Navigate to URL Use Case](docs/PopUp_Blocking.jpg)

![Password Input Misuse Case](docs/Password_Input_Misuse_Case.drawio.png)
Security requirements:
 - Hide the text inside Input elements of type `Password` by default.
 - Support Content Security Policy headers inside HTTP responses from sites.
 - Support the `script-src` directive (and similar derived directives) to specify specific allowed file hashes.

Implemented security features:
Supports Content Security Policy, supports script-src, script-src-elem, and script-src-attr. Supports using nonce values for use with dynamic, inline scripts. Ladybird seems to have the security requirements derived from this diagram implemented fully. When inspecting the code, I found comments specifying the W3C standard number for whatever was being implemented in that block, so it seems to be a thorough implementation that goes through the standard for all of these items.

I am investigating whether password inputs are obscured. I did not find any obvious references to this behavior within the HTMLInputElement.cpp file, but I will build Ladybird locally and experiment myself.

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

## Project board:
[Link](https://github.com/users/chauler/projects/3)


---

## Reflection:

Alex's reflection:
I found this assignment to be useful for getting me in the right mindset for useful security analysis. I started this class thinking "buffer overflows" and "modifying memory" - rarer and relatively difficult types of attacks. Trying to come up with good use/misuse cases helped me to remember the small security features we usually don't think of - in this case, it was obscuring password inputs. Starting from that foundation, I was more easily able to build out a diagram of more realistic threats.

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
  
## Part 2:
