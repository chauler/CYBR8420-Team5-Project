# CYBR8420-Team5-Project

## The Software
The software chosen for this project is the Ladybird browser, located at [this website](https://ladybird.org/) and [this Github repository](https://github.com/LadybirdBrowser/ladybird).

## Operational Environment
Link to open source project: https://github.com/LadybirdBrowser/ladybird

The specific isolatio issue that we are considering contributing to is:
 https://github.com/LadybirdBrowser/ladybird/issues/302

The inferred operational environment for using the LadyBird browser would either be home or school. 
There would be limited use of a browser such as LabyBird in professional or government environments due to 
the use of predominant browsers such as Google Chrome and Microsoft Edge due to the wide adoption and reliability of these browsers within these environments.

In terms of the hypothetical enviornment systerm diagram, the system of interest would be the LadyBird browser. The system elements of a typical browser include: User Interface, Browser Engine, Rendering Engine, JavaScript Engine, Networking Module, Data Storage, and Security Module. These elements were pulled from initial research on the common components of browers and a good reference could be: https://educatecomputer.com/what-is-a-web-browser/ . 

Enabling systems for the browser would be the computer operating system, compilers, network infrastructure, certificate authorities. The other systems could be the various websites that the browser will visit, both safe and malicious.

Existing security features include the use of separate processes for each tab, including separate renderers and Frame objects for individual pages and iframes. The software also features a JavaScript engine without compilation

The threats that are perceived by the user are malicious websites with Cross Site Scripting attacks.

Site Isolation would limit the blast radius of a Cross Site Scripting attack. If you visited a malicious website, the site isolation inside of the browser would limit the attack to only the singe tab. It would not allow the malicious website to see data that was exposed in the other browser tabs.

## Motivation
Our motivation for selecting this project was the large scope and heavy use of internal code giving lots of potential for security contribution. The many types of input (user interaction, IPC, HTML pages, other network communications) provide many potential attack vectors. Also, the large number of contributors and pull requests show a project that is built for outside contribution.

## Project Description
Ladybird is an open-source cross-platform browser forked from another open-source project, SerenityOS. SerenityOS is a from-scratch operating system project, and Ladybird inherited much of its code. This from-scratch philosophy means that Ladybird has its own JavaScript engine, cryptography libraries, and handles its own text encoding, regex parsing, rendering, etc.

The project was started by Andreas Kling and is managed by a non-profit organization dedicated to the governance of the project. The project has over a thousand contributors throughout its history and regularly receives hundreds of pull requests from dozens of contributors per month.

The software is mostly written in C++ with some newer code written in Swift. Ladybird runs on Unix-like operating systems, including Linux and MacOS. [Documentation](https://github.com/LadybirdBrowser/ladybird/tree/master/Documentation) can be found in the `/Documentation` directory of the repository, including extensive guides on building the project and contributing. 

The software uses a BSD-2 license. Contribution guidelines cover code style, interaction with other contributors, and rules for project management - these rules include guidelines for reporting security issues, which state that public Github issues may be utilized as the software is not yet officially released. Contributions are expected to use C++23 and follow existing code style. AI assisted contributions are allowed, but use of AI in issue generation or other interaction with humans in the project is not allowed.

### Project's Security History
The project's origins in SerenityOS mean that many security topics were put off for later in favor of other progress. However, the project has begun to take itself more seriously, with an official governance structure and an Alpha release planned for Summer 2026. This can be seen with moves such as the planned adoption of Swift as a successor to C++ for memory safety purposes. Issues such as cross-site scripting and request forgery, content security policy violations, and iframe sandboxing are acknowledged issues that have not been dealt with at this point. The project is being architected with security in mind - see the below image for the planned process architecture of a single browser instance. Further explanation of the architecture can be found [here](https://github.com/LadybirdBrowser/ladybird/blob/bbcd8bd97ca5b3bbf93a6c5183b89bd887c67e54/Documentation/ProcessArchitecture.md#L4)

![Process Architecture](https://github.com/LadybirdBrowser/ladybird/blob/bbcd8bd97ca5b3bbf93a6c5183b89bd887c67e54/Documentation/Images/processes.png)

There is not much information readily available regarding patched security vulnerabilities, at least through the typical avenue of public Github issues.

**Observations from Open Hub:**

- Open Hub reports that no known vulnerabilities have been listed across Ladybird’s recent versions.

- However, its Security Confidence Index is rated as “Poor”, which reflects limited or no history of published CVEs, making it harder to judge the project’s responsiveness to security issues.

- The codebase is large (~774,000 lines of C++ and related languages), which increases the attack surface and potential for hidden vulnerabilities.

- With ~1,400 contributors and thousands of commits per year, active development is ongoing, but this also introduces a higher chance of inconsistent security practices if strong code review isn’t enforced.

**Systems Engineering View**

<img width="842" height="661" alt="image" src="https://github.com/user-attachments/assets/fd5e3b54-ee23-426b-9e88-6a6fbbb51bdc" />


## Individual Reflections
From this assignment, I learned more about the details and makeup of a web browser. I use a web browser daily, but I rarely think about components and the details of how the browser works. In order to build out the system engineering view, I needed to know the components and architecture of a web browser. Having a good understanding of how the software package works helps identify misuse cases and potential software assurance improvements. The examples of system engineering views from previous years from past projects were helpful to make sure we were including the correct level of detail in our system engineering view.

I learned about the threats users face in browsers, especially cross-site scripting and the importance of site isolation. Seeing how Ladybird is approaching these challenges gave me a better appreciation for how security evolves in new software and how governance decisions, like adopting Swift for memory safety, influence its future direction. I also found it valuable to explore Open Hub analytics, where metrics such as lines of code, contributor activity, and the security confidence index helped me connect the project’s scale to its maturity and risks. Using these external tools provided quantitative insights into code quality and security posture, complementing the qualitative research and making the overall analysis more comprehensive.

This assignment was eye-opening predominantly surrounding how much open-source software there is. I also had to learn how to explore security related topics on a project with little information on how the software even functions as a whole, which was difficult, but in the case of the ladybird browser, the issue sorting in github pointed me in a better direction. Creating the diagram was also insightful because I had to learn how a web-browser works down to its foundation and will need to do a lot more learning to get the best understanding I can about ladybird. 
