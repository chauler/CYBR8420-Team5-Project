## Top Claim #1 (Jason) - Browser protect user-stored data
![Protect User-Stored Data Assurance Case](docs/assurance_cases/Navigate_URL_Assurance_Case_Revised.jpg)
=======


# Part 1

## Code-review strategy
As a team, we decided to use a hybrid approach to our code review of the ladybird browser codebase. The ladybird browser is a fairly large and complex codebase to analyze. Each of us took the misuse and assurance cases that we created in previous assignments and focused on the code that provided those capabilities. We are doing a hybrid of scenario based and weakness based code review. 

The code being C++ was going to add some complexity for automated scanning tools to perform a static analysis of the code because the tools would need to compile the code. We addressed these challenges by each team member selecting a different scanning tool to attempt. Between the five team members, we figured we should be able to get at least one or two automated code scanning tool results. We were able to successfully use Checkov, ..., but were not able to get SonarQube to work. We also performed manual code reviews to bridge the gap of static analysis for the automated tools and in total put together a list of CWEs most relevant to our assurance cases.  

## CWE Analysis

Summary of the manual code reviews that were performed

Summary of the automated code reviews that were performed

# Part 2
There is a lot of repeat in part 2 of what was asked for in part 1.  In part 1, we include a summary of the manual and automated code review findings. In part 2 we add in the perceived risk in our hypothetical operational environments (which I think is a professional workspace)
For the planned contribution to the open-sourced project, we can pick one of the vulnerabilities that the automated code scanning identified. 