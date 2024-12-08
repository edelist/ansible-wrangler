<div align="center">

# Ansible Wrangler

</div>
## Table of Contents
- [Members](#members)
- [Progress](#progress)
- [Setup](#setup)
- [Project Overview](#project-overview)
- [Project Visions and Overall Goals](#project-visions-and-overall-goals)
- [Users and Personas of the Project](#users-and-personas)
- [Scope and Features](#scope-and-features)
- [Solution Concept and General Architecture](#solution-concept-and-general-architecture)
- [Design Implications and Discussion](#design-implications-and-discussion)
- [Acceptance Criteria](#acceptance-criteria)
- [Release Planning](#release-planning)
- [General Comments](#general-comments)
- [Resources](#resources)

## Members
| Name | Role | Contact Information |
| :---: | :---: | :---: |
| Ryan Cook | Mentor  | rcook@redhat.com |
| Albert Zhao | Contributor | albertz@bu.edu |
| Reza Sajjadi | Contributor | sajjadi@bu.edu |
| Jack Edelist | Contributor | edelist@bu.edu |
| Arthur Hua | Contributor | ahua102@bu.edu |
| Michael Barany | Contributor | mbarany@bu.edu |
| Alireza Roshandel | Contributor | alirezad@bu.edu |

## Progress
[Sprint 1 Demo](https://drive.google.com/drive/folders/19N2t67B59QdDt1MnHAf7CkfM7lJ14rJK?usp=sharing)

[Sprint 2 Demo](https://drive.google.com/drive/folders/1Zj_zdQ7Hdqzi7-cDt-tku3rnGXbvKtWH?usp=sharing)

[Sprint 3 Demo](https://drive.google.com/drive/folders/1ChZvkJFYUFSo1Nl7DjrvoWRS28i35qFZ?usp=sharing)

[Sprint 4 Demo](https://drive.google.com/drive/folders/1vaU_AqYLK72W9cyWqbyPGBEtvsVrENK1?usp=sharing)

[Sprint 5 Demo](https://drive.google.com/drive/folders/1G4xL2IC2cTTBUZB7TVk2pJHk90gwYuo-?usp=drive_link)

## Setup
1. Clone the main branch of the repository to your machine.
2. Ensure you have the following prerequisites:
   - A **ServiceNow Developer Instance**.
   - An **AWX Instance**.
3. Embed your credentials into the configuration files as specified in the documentation.
4. Run the `start_wrangler.sh` script with root privileges.
5. **Note**: If Conda is not installed on your machine, you may need to run the script **twice** for proper setup.

## Project Overview
The Ansible Wrangler Automation project is a fully automated solution that integrates ServiceNow incident management with Ansible playbook generation and Github repository management.

## Project Visions and Overall Goals
Our vision is to reduce manual efforts involved in incident management and solution playbook creation by streamlining the process of generating, managing, and deploying playbooks in Ansible in response to incidents reported by ServiceNow users. 

This system:
- Automates the process of creating appropriate Ansible playbooks via the Ollama AI model based on the user's reported incident.
- It also checks for pre-existing playbooks in the GitHub repository using RAG. It will then determine if a suitable playbook exists and return it to the user. 
- If no suitable playbook exists, the system will dynamically generate one based on its understanding of the error, the provided description, and historical data similar to this issue using Ollama.
- The ideal playbook will then automatically be fed into Redhat's Ansible to be verified, run, and deployed. And the ideal playbook will be stored into a git repo for future use. 

Overall, this project should reduce the time between reporting an error and getting feedback from another software developer/or IT personal by having a solution be generated immediately. This should allow more people to be able to use Ansible and fix any issues without having in-depth knowledge about how to fine-tune their system and help teams efficiently address issues.

## Users and Personas
**Site Reliability Engineer (SRE)**  

**Key Characteristics:** Site Reliability Engineers are responsible for ensuring the high availability, performance, and security of infrastructure. They frequently respond to incidents and investigate operational issues, often utilizing tools like Ansible along with monitoring platforms such as Prometheus and Grafana. SREs need rapid and reliable solutions for infrastructure problems and are comfortable working with playbooks and scripts.  

**Primary Actions:** In this project, SREs receive alerts about system issues, review and approve Ansible playbooks generated by Ansible Wrangler, and initiate their execution to resolve detected issues. They also provide valuable feedback on playbook performance to improve future remediations.
  
**System Administrator (SysAdmin)**  

**Key Characteristics:** System Administrators manage and maintain the core Linux and Kubernetes infrastructure and are often the first point of contact for infrastructure-related issues. They oversee repositories of standard playbooks, updating them as system changes occur, and seek opportunities to streamline repetitive tasks through automation. 

**Primary Actions:** For this project, SysAdmins review and refine playbooks generated by Ansible Wrangler before merging them into production. They suggest improvements to existing playbooks for more efficient operation and ensure that system updates or configurations are effectively integrated into playbooks.
  
**Security Engineer**  

**Key Characteristics:** Security Engineers are responsible for ensuring the security and compliance of the operating system and infrastructure. They identify and address security incidents, vulnerabilities, and misconfigurations, and review the security implications of proposed playbooks to maintain compliance.  

**Primary Actions:** In this project, Security Engineers audit generated playbooks to ensure adherence to security policies, validate that remediations do not introduce new vulnerabilities or compliance issues, and provide feedback to enhance the security aspects of the automation process.
  
**DevOps Engineer**  

**Key Characteristics:** DevOps Engineers act as a bridge between development and operations, maintaining CI/CD pipelines and ensuring the integration of playbooks into deployment workflows. They manage Git repositories, ensure standardization, and automate repetitive tasks.  

**Primary Actions:** In this project, DevOps Engineers manage and merge playbooks into Git repositories while automating their execution as part of CI/CD pipelines. They test and validate proposed playbooks, ensuring reliability and seamless integration into continuous automation workflows.


## Scope and Features
**ServiceNow Integration**
- Automatically connects to ServiceNow to fetch unresolved incidents using specified user credentials.
- Updates the state of incidents in ServiceNow after processing, including adding comments, tracking incident states, and marking them as resolved when successfully addressed.
- ServiceNow Interface (SNOW): Users will be able to submit incidents and see solution/playbook in the incident activity section.
  
**AI-Generated Playbooks**
- Generates Ansible playbooks using the LLaMA language model when no suitable playbook is found in the repository.
- Provides users with the option to either retrieve a relevant existing playbook or request the generation of a new one.
  
**Existing Playbook Retrieval**
- Implements a FAISS-based retrieval system to search a repository of indexed Ansible playbooks.
- Ranks and retrieves the top-k most relevant playbooks based on the incident description to minimize redundant generation.
  
**GitHub Integration**
- Automatically saves generated or selected playbooks to a designated GitHub repository.
- Commits changes, pushes them to the specified branch, and synchronizes with AWX for deployment.
  
**AWX Automation**
- We integrated our system with AWX to:
  - Trigger project updates.
  - Create job templates for new playbooks.
  - Launch and track jobs for Ansible playbook execution.
- Ensures seamless deployment of playbooks to manage servers and resolve incidents on cloud.
  
**Interactive Incident Handling**
- We have the ability to retrieve the most recent comment from the activity section in an incident report on ServiceNow
- Monitors comments on ServiceNow incidents to allow users to:
  - Search for existing playbooks.
  - Generate new playbooks dynamically using AI.
  - Select and deploy a playbook by responding to the system’s prompts.
    
**End-to-end Pipeline**
- A fully automated pipeline that spans incident detection, playbook generation/retrieval, deployment, and resolution tracking.

## Additional Features to be Considered
These are some of the features that we could implement after the completion of our main objectives. 
- Incident Feedback Loop: 
  - implement feedback mechanism for SREs to rate effectiveness of generated playbooks
- Incident categorization: 
  - use generative AI to categorize incidents into different severity levels and assign relevant actions or workflows automatically
- ChatOps Integration: 
  - integrate with Slack, Microsoft Teams or other chat platforms to allow playbook generation within team chats, or direct access through 1-on-1 chat with an Ansible Wranger “bot”
- Further Cloud and Queue Integration:
  - Using different queue structures to prioritize different tasks and requests when handling a large load of incident requests. 

## Solution Concept and General Architecture
This project follows a modular architecture that integrates several components to automate incident handling and playbook generation. Below is a general architectural overview and workflow of the project: 
Architectural components:
1. ServiceNow: This is the starting point of the workflow, where incidents are generated and managed. We will utilize ServiceNow API and SNOW interface to fetch incidents.
2. Automation: The core of this automation engine that:
   - Incident Retrieval: get incidents created by users from ServiceNow.
   - Playbook Search Module: searches for existing playbooks in a GitHub repo to check for a match with the incident description
   - Llama Interaction: if no match is found, this module generates a new Ansible playbook using the Llama model based on the description of the incident.
   - GitHub API Integration: Automatically submits a pull request to a specific GitHub repo for the newly generated playbook. This stores all the existing playbooks.
   - Incident State Updater: Updates the state of the incident in ServiceNow to notify the user about the action taken (through ServiceNow activity/comments) 
3. GitHub Repository: A version control system where the existing playbooks are stored, and new playbooks are committed. We will be using the GitHub API to manage the repository, branches, commits, and pull requests. 
4. Llama: The natural language processing model (GPT) that interprets incident descriptions and generates Ansible playbooks.

<img width="811" alt="image" src="https://github.com/user-attachments/assets/eaec2984-7cc5-47b4-95fd-66038453f5c6">

This solution is scalable, allowing it to be expanded or enhanced with more features, such as intelligent playbook customization, based on the timeline and evolving project needs.

Our module communication chart:

<img width="963" alt="image" src="https://github.com/user-attachments/assets/11055652-c54b-43c9-b44f-d41039fee3bb">

Detailed Process chart:

<img width="757" alt="image" src="https://github.com/user-attachments/assets/c24c2201-45fa-4e91-a96a-ad9e7a189548">


## Design Implications and Discussion
1. **API-Driven Architecture**: The decision to use an API-driven architecture ensures flexibility and scalability. By integrating with ServiceNow, Gen AI API, and GitHub APIs, the system is modular and can easily be expanded to include other services in the future.
2. **AI-Driven Playbook Generation**: allows the system to automatically create customized and relevant Ansible playbooks based on incident descriptions. This reduces manual intervention and accelerates incident resolution. Using a pre-trained model also enhances efficiency while ensuring that the system can adapt to various types of incidents with minimal human input.
3. **Automation-First Approach**: The whole point of this project is to use automation to speed up incident report. By eliminating manual steps, the system can quickly respond to incidents, reducing resolution times and ensuring a consistent, repeatable workflow.

## Acceptance Criteria
We want to make sure that the core functionality of the Ansible Wrangler Automation project is met. To ensure this, essential features and capabilities must be implemented and verified for this project to be complete. Essential features include:
- ServiceNow Incident Retrieval
- Existing Playbook search
- OpenAI (or other language model) playbook generation
- GitHub Integration
- ServiceNow incident update

## Release Planning 
This project will be delivered in a series of iterative releases, each introducing new features and functionality incrementally. 

### Release overview
Each release will focus on a specific set of features, starting with the core functionality of the system and expanding to include advanced capabilities such as playbook validation, error handling, and enhanced integration. 

#### First Sprint Study, Research and Core Infrastructure Setup

In the first sprint, we focused on researching all the necessary frameworks, structures, and APIs for this project. We researched multiple LLMS (OpenAI and Llama) to see which model is faster and more efficient for Ansible playbook generation. We also researched Rest API to see how data can be transferred between different modules within our project. We also investigated different cloud services and structures to apply the best cloud application. 

#### Second Sprint: Core Functionality

The goal of the second sprint is to establish the core structure of the Ansible Wrangler Automation system and implement basic functionalities that enable the system to handle incidents efficiently. 
- Develop a simple and intuitive front-end interface where users can submit incident requests. This interface will collect relevant information such as the error description, code snippets (if applicable), urgency, state of the issue, and any additional context needed to resolve the issue.
  - This interface could be integrated into existing chat platforms like Slack, or it could be a standalone web form that communicates with the backend system via REST API calls.
- Implement a search algorithm to scan the existing repository of playbooks for a match.
- Integrate a LLaMA Language Model dynamically generate new Ansible playbooks when no suitable existing playbook is found.
- The system will analyze the incident description and any provided code to create a customized playbook. This playbook will be in YAML format and structured to resolve the issue step-by-step.
  

#### Third Sprint: GitHub Integration and Pull Requests 

#### Fourth Sprint: Advanced Features and Stretch Goals

Key Deliverables Per Release
- Release 1: Core system infrastructure, logging, and initial API connections.
- Release 2: Full playbook generation pipeline, including incident retrieval, GPT-based generation, and playbook commits.
- Release 3: GitHub integration with pull request creation, validation, and error handling.
- Release 4: Advanced features, scalability improvements, and user notifications.

Each sprint will conclude with a sprint review, where feedback will be collected to guide the next sprint. The first release is expected to provide the foundational elements of the system, with each subsequent release building upon it and adding more advanced features.


## General Comments
We plan to scale this project based on the available timeline, with the potential to expand its functionality and incorporate additional features. As the project progresses, we will evaluate opportunities for enhanced automation, improved playbook matching algorithms, and integration with other platforms to make sure the system remains flexible and adaptable to future needs/progress. 

## Resources
https://github.com/cooktheryan/wrangler

https://github.com/cooktheryan/wrangler-out/pull/44/files

https://docs.ansible.com/

