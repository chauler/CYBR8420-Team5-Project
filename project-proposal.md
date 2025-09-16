# CYBR8420-Team5-Project

## The Software
The software chosen for this project is the Ladybird browser, located at [this website](https://ladybird.org/) and [this Github repository](https://github.com/LadybirdBrowser/ladybird).

## Operational Environment
Link to open source project: https://github.com/LadybirdBrowser/ladybird

Link to Site Isolation issue that we are considering contributing to: https://github.com/LadybirdBrowser/ladybird/issues/302

I think the most likely operational environment for using the LadyBird browser is either home or school. Realistically, most professional or government environments are going to use one of the predominant browsers (in my opinion)

For the hypothetical environment system diagram, the system of interest would be the LadyBird browser. The system elements of a typical browser are User Interface, Browser Engine, Rendering Engine, JavaScript Engine, Networking Module, Data Storage, and Security Module. I had to do some research on what the common components of a browser were. This is a good reference: https://educatecomputer.com/what-is-a-web-browser/

Enabling systems for the browser would be the computer operating system, compilers, network infrastructure, certificate authorities. I think the other systems could be the various websites that the browser will visit, both safe and malicious.


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

## Individual Reflections
