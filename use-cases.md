# Use Cases

![Navigate to URL Use Case](docs/UseCase_Navigate_to_URL.svg)

![Navigate to URL Use Case](docs/secure%20site%20loading.png)

![Navigate to URL Use Case](docs/UseCase_File_Download.png)

![Navigate to URL Use Case](docs/PopUp_Blocking.jpg)

![Password Input Misuse Case](docs/Password_Input_Misuse_Case.drawio.png)
Security requirements:
 - Hide the text inside Input elements of type `Password` by default.
 - Support Content Security Policy headers inside HTTP responses from sites.
 - Support the `script-src` directive (and similar derived directives) to specify specific allowed file hashes.

 Implemented security features:
 Supports Content Security Policy, supports script-src, script-src-elem, and script-src-attr. Supports using nonce values for use with dynamic, inline scripts.

 I am investigating whether password inputs are obscured. I did not find any obvious references to this behavior within the HTMLInputElement.cpp file, but I will build Ladybird locally and experiment myself.