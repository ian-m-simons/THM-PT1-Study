### Purpose
The purpose of a penetration testing framework is: to create a systematic methodology which ensures that testing is thorough, consistent, addresses appropriate compliance frameworks, is well structured, and within scope (especially important both for legal reasons, and likely for the PT1 exam)
Think of it like a building inspector following a code-compliance checklist.
We will explore the following frameworks
- [[#Open Source Security Testing Methodology Manual (OSSTMM)]]
- [[#OWASP Web Security Testing Guide (WSTG)|OWASP Security Testing Guide (WSTG)]]
- [[#NIST SP 800-115|NIST Special Publication 800-115]]
- [[#Penetration Testing Execution Standard (PTES)]]
- [[#Information Systems Security Assessment Framework (ISSAF)]]
We will also review the [[#MITRE ATT&CK]] framework as well as look over the [[#Other Notable Frameworks]] including:
- WASC threat classification
- CSA cloud control matrix
- OWASP Mobile Application Security Testing Guide (MASTG)
- PCI DSS Penetration Testing Guidelines
- CBEST Framework
### Open Source Security Testing Methodology Manual (OSSTMM)
The Goal of the OSSTMM is to prioritize metrics over opinions. Rather than delivering subjective risk judgements, OSSTMM produces quantifiable, verifiable, and repeatable results.
OSSTMM organizes testing into five security channels:
- Human Security (HUMSEC)
- Physical Security (PHYSSEC)
- Wireless Communications (SPECSEC)
- Telecommunications (COMSEC)
- Data Networks (DATASEC)
At the Heart of OSSTMM's approach is the Risk Assesment Values (RAVs) which measures the balance between the total attack surface (exposure) and the controls in place for said exposure. Higher numbers indicate residual risk, lower numbers
The goal of the numeric output is that two testers assessing the target should arrive at the same, or near the same result.
##### OSSTMM testing cycle phases

###### Phase 1 Induction
covers enumeration, and verification, you map what exists and confirm it is real. The goal is to build an inventory of targets
###### Phase 2 Interaction
covers qualification, and quantification, you actively probe the verified assets and asses their relevance. The goal of this phase is to feed directly into the attack surface calculation.
###### Phase 3 Inquiry
covers privilege and verification escalation, this is where you actually attack the thing
###### Phase 4 Intervention
Quarantine, audit, and enticement. Address findings and examine the broader control environment.
##### Deliverables
OSSTMM prescribes the Security Test Audit Report (STAR) format for deliverables, enforcing consistency and enabling cross-team comparability.
### OWASP Web Security Testing Guide (WSTG)
The OWASP WSTG is a comprehensive, community-driven framework that organizes web application testing into over 90 test cases grouped across twelve categories. Categories include: Information gathering, configuration and deployment management, identity management, authentication, authorization, session management, input validation, error handling, cryptography, business logic, client-side testing, and API testing. each category contains numbered test cases with step by step guidance on what to test and how to test it
the approach is meant to help testers focus their efforts where they matter most
##### OWASP WSTG testing cycle phases
phases of testing align across the five phases of the Software Development Life Cycle (SDLC)
###### Phase 1 Before Development Begins
Security requirements and regulatory obligations are established
###### Phase 2 During Definition and Design
The application architecture is reviewed for security BEFORE any code is written.
###### Phase 3 During Development
Code is vetted through walkthroughs and reviews AS IT IS DEVELOPED,  using WSTG test cases etc
###### Phase 4 During Deployment
Security controls are verified and tested BEFORE the application enters production. in this step you set up a testing environment to match the production environment and perform a Pentest before deploying
###### Phase 5 During Maintenance and Operations
Security is maintained post-launch through periodic health checks, especially after updates
##### Pros and Cons
###### Pros
- Very thorough
- Practical
- Continuously updated
###### Cons
 - Full implementation impractical if facing resource constraints
 - Some tests require strong specialization such as crypto analysis or business logic
 - can create a checklist mentality

### NIST SP 800-115
Titled "Technical Guide to Information Security Testing and Assessment" this document provides a foundational framework for systematically evaluating the security posture of information security. NIST stands for National Institute of Standards and Technology. While not strictly speaking a penetration testing framework, it covers the full spectrum of security testing and assessment techniques. It treats pentesting as one of many steps used to validate that security controls are working as intended.
##### NIST SP 800-115 testing cycle phases
###### Phase 1: Planning
Here you will formally lay out the scope and set the rules of engagement (ROEs) should include a formal test plan that all stakeholders sign off on.
###### Phase 2: Execution
this is where active testing happens  NIST SP 800-115 groups execution activities into four technique categories
- Review Techniques -- Examining docs, policies sys configs, and rule sets
- Target Identification and Analysis -- Discovering and fingerprinting live hosts, open ports, and running services
- Target Vulnerability Validation -- Confirming that Identified weaknesses are real and exploitable, not false positives.
- Penetration Testing -- Simulating an adversarial attack to test the depth of possible exploitation
###### Phase 3: Post-Testing
this is your after action report and lessons learned. Analyze results, prioritize risk, and deliver actionable remediation strategies. 
##### Pros and Cons
###### Pros 
- flexible
- institutional credibility 
- standardization
###### Cons
- does not enforce audit frequencies or penalties (it is guidance not regulation)
- broad skillset requirements
### Penetration Testing Execution Standard (PTES)

This is the most commonly used standard workflow used by working pentesters. The goal of this standard was to define what a real penetration test looks like from end-to-end. The Seven phases map directly to the lifecycle of an engagement making it good for Jr. testers
##### PTES Testing Cycle Phases
###### Phase 1: Pre-engagement Interactions
Here you establish scope, ROEs, testing windows, emergency contacts, and a "get out of jail free" letter authorizing the test.  unclear scoping is the number one source of legal and professional disputes
###### Phase 2: Intelligence Gathering
Using both active and passive reconnaissance gather as much intelligence on the target as possible. Remember to stay in scope. 
###### Phase 3: Threat Modeling
Using the intelligence you gathered, Identify the Most valuable targets and most likely attack paths. 
###### Phase 4: Vulnerability Analysis
Systematically identify weaknesses that could enable the attack paths from your threat model. 
###### Phase 5: Exploitation
Attempt to exploit the confirmed vulnerabilities. Important to remember the point is business impact not "popping boxes" for the sake of it.
###### Phase 6: Post-Exploitation
After paining access, determine the real-world impact. This is where you translate technical findings into business risk
###### Phase 7: Reporting
Deliver the findings in a structured report with two audiences in mind: Leadership and Technical staff. Use the executive summary to to communicate business risk in plan language for leadership. Use the technical report to provide details about what actually needs to fixed and how to reproduce findings. Ensure you include: exact exploitation steps, affected hosts, evidence screenshots, and prioritized remediation guidance.
##### Pros and Cons
###### Pros
- practical end-to-end structure
- excellent learning framework for jr testers
- pre-engagement steps are particularly well done
###### Cons
- has not been formally updated in years
- does reference outdated tools and techniques
- while the methodology and phases are sound, technicians should update/supplement tool specific guidance.
- lacks quantitative metrics
### Information Systems Security Assessment Framework (ISSAF)
open source framework designed to evaluate network, system, and application security. It is no longer actively maintained. while the methodology and phase structure remain instructive, the tool specific guidance is long outdated. This is on of the clearest representations of how an attacker progresses through a target environment. 
##### ISSAF Testing Cycle Phases
###### Phase 1: Planning and Preparation
set the scope, establish escalation protocols, emergency contacts, identify constraints etc
###### Phase 2: Assessment
core nine-step model 
- Information Gathering -- Active and Passive recon
- Network Mapping -- Map the Live network topology 
- Vulnerability Identification -- scan the mapped assets for weaknesses
- Penetration -- attempt initial exploitation
- Gaining Access and Privilege Escalation -- Escalate from initial access to higher privileges
- Enumerating Further -- using elevated access re-enumerate and see what you can reach
- Compromise Remote Users/Sites (lateral movement) -- move laterally to other systems
- Maintaining Access -- establish persistent access to demonstrate that a real attacker could retain their foothold. 
- Covering tracks -- Demonstrate how an attacker would erase evidence
###### Phase 3: Reporting and Cleanup
compile findings into report, prioritize business impact. remove any test artifacts, revoke temporary accounts and ensure with organization that no testing residue remains. 
##### Pros and Cons
###### Pros
- provides clear mental model for how real world attacks unfold
- excellent educational tool
 ###### Cons
 - no longer maintained
 - tool guidance is basically trash at this point
### MITRE ATT&CK
Used to systematically catalog the specific TTPs that real world adversaries use.
ATT&CK stands for Adversarial Tactics, Techniques, and Common Knowledge. It was developed and is maintained by the MITRE Corporation. It is a knowledge base of adversary behavior build from real-world observations. 
notably MITRE ATT&CK does not tell you how to run a pentest. It DOES provide common language for describing what you found during a test conducted using any framework.
### Other Notable Frameworks
Most of these tend to fill a specific niche 
##### WASC Threat Classification
- developed by Web Application Security Consortium (WASC)
- taxonomy for categorizing Web application vulnerabilities
- largely replaced by OWASP Top Ten and WSTG
##### CSA Cloud Controls Matrix (CCM)
- Published by the Cloud Security Alliance (CSA) 
- Provides a cyber security controls framework for cloud computing
- Aligns domains with major standards such as ISO, NIST, etc 
- not a pen testing framework, it is a governance and compliance tools
##### OWASP Mobile Application Security Testing Guide (MASTG)
- Mobile counterpart to WSTG 
- Maintained by OWASP
- Used alongside OWASP Mobile Application Security Verification Standard (MASVS)
##### PCI DSS Penetration Testing Guidelines
- Defined by the Payment Card Industry Data Security Standard
- Regulatory mandate for any organization that processes, stores or transmits cardholder data. 
- Testing is required annually and after major infrastructure changes. 
##### CBEST Framework
- Threat-Intelligence-led penetration testing framework designed specifically for UK financial institutions. 
- begins with threat intel phase that identifies most relevant threat actors and attack scenarios. 
- then you use those to simulate attacks
##### Parting Shots
Framework Selection should always be driven by context, a hospital would not use CBEST. Knowing the landscape means recognizing which framework to use fora given engagement 
### Choosing the Right Framework
Frameworks are, at the end of the day, tools. You always want to make sure you are selecting the correct tool 
##### Framework Selection Criteria
###### Engagement Scope and Target Type
- What are you testing? web app? mobile app?
- Are we attacking humans or just machines?
###### Regulatory and Compliance Requirements
- What required standards are there? PCI DSS? CBEST?
###### Need for Quantifiable, and Repeatable Results
- Great for when multiple assessors are involved
- Great for comparing results over time (are we improving?)
###### Team Expertise and Available Resources
- These are practical constraints
- OSSTMM is great but requires deep familiarity with its metrics system. 
##### Parting Shots
- Pretty rare that only one of these frameworks will come into play
- Usually you will have a Primary like PTES with secondaries used for compliance or mapping etc.
- The ability to recognize which framework applies and howe different frameworks complement each other is a skill that distinguishes a methodical penetration tester from someone who just runs tools