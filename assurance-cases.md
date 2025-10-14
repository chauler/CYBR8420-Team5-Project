# Assurance Cases

## Top Claim #1 (Jason) - Browser protect user-stored data
![Protect User-Stored Data Assurance Case](docs/Navigate_URL_Assurance_Case.svg)
=======


## Top Claim #2 (Logan) - 

## Top Claim #3 (Arun) - 

## Top Claim #4 (Alex) - 

## Top Claim #5 (Godwin) - 


<hr style="border-top: 6px dotted white;">

## AI Prompting
The following AI prompt was used to refine and improve assurance claim for protecting user-stored data
>You are an expert software security requirement engineer.\
>Your job is to suggest corrections or improvements in the phrasing of assurance claims. Claims concern critical properties that are risk-related. High confidence is needed in their realization.\
>A claim is always worded with a predicate. Avoid claims about the supporting method/techniques.\
>The rebuttles for the claim directly follow. A good rebuttal is characterized by its clarity, precision, and logical structure. It should directly address the key points of the opposing argument, refute logical fallacies, and provide strong evidence.\
>""" \
>These claims and rebuttles have already been developed:\
>Top-Claim C1: Browser maintains confidentiality of user data stored in the ladybird browser\
>Rebuttal R1: Insufficient Protection of Stored Credentials and Local Data\
>Rebuttal R2: Malicious Web Content Can Exploit Browser Flaws\
>Sub-Claim C2: The browser encrypts sensitive data at rest and enforces strict access control for local access\
>Rebuttal R3: Encryption and access control may be miscofigured or incomplete\
>Sub-Claim C3: Sandboxing and Enforcing strict content security policies (CSP) block malicious web content from accessing sensitive dataThe browser encrypts sensitive data at rest and enforces strict access control for local access\
>Rebuttal R4: Browser executes third-party scripts that expose user data\
>Sub-Claim C4: Sensitive data is not stored or leaked\
>Sub-Claim C5: Third-party scripts cannot access private user data\
>""" \
>Goal number 1: Critique the existing assurance claims and rebuttles. Suggest edits to strengthen an argement that would convince stakeholders in my assumed/hypothetical operational environment.\
>Goal number 2: Iterate additional or missing claims and rebuttles \
>Goal number 3: Help identify what evidene is needed to prove subclaims

<hr style="border-top: 6px dotted white;">

## Discussion

The ladybird browser open source project incldues [LibCrypto](https://github.com/LadybirdBrowser/ladybird/tree/master/Libraries/LibCrypto/Cipher). folder with code for AES encryption and decryption. This helps support the Browser protects user stored data sub-claim C2. The project also includes code that check for cross origin isolation in a [LibURL/Origin](https://github.com/LadybirdBrowser/ladybird/blob/master/Libraries/LibURL/Origin.h) class, which supports sub-claim C3. Addition analysis will need to be done to validate the details of how the code is implemented, but the inclusion of this code helps build our assurance cases. 
