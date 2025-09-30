# Use Cases

![Navigate to URL Use Case](docs/secure%20site%20loading.png)

![Navigate to URL Use Case](docs/UseCase_File_Download.png)

![Navigate to URL Use Case](docs/PopUp_Blocking.jpg)

![Password Input Misuse Case](docs/Password_Input_Misuse_Case.drawio.png)

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
