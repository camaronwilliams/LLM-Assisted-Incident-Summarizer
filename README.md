# LLM-Assisted Incident Summarizer

## Project Overview
This project automates cloud security incident response using Azure Sentinel, Logic Apps, and AI.  
When a VM is created or deleted, the system automatically:  

1. Detects the incident via Azure Sentinel.  
2. Summarizes the incident using an AI model (LLM).  
3. Sends a ticket or summary to Microsoft Teams and/or email.  

---

## Tech Stack
- **Cloud & Security:** Azure Sentinel, Azure Logic Apps, Key Vault  
- **AI:** Azure OpenAI / simulated LLM endpoint  
- **Automation & Integration:** HTTP actions, SOAR playbooks  
- **Collaboration:** Microsoft Teams  

---

## Workflow Diagram
![Workflow Diagram](diagrams/WorkflowDiagram.png)  

---

## Screenshots
- Sentinel Analytics Rule: ![Sentinel Rule](screenshots/SentinelRule.png)  
- Logic App Designer: ![Logic App](screenshots/LogicAppDesigner.png)  
- Teams Output: ![Teams Output](screenshots/TeamsOutput.png)  

---

## How to Test
1. Trigger the VM create/delete event.  
2. Sentinel generates an incident.  
3. Logic App playbook sends incident to LLM (or dummy endpoint).  
4. Summary is posted to Teams or email.  

---

## Skills Demonstrated
- Cloud Security & SIEM  
- SOAR Automation  
- AI-assisted summarization  
- REST API & HTTP integration  

---

## Notes
- Logic App exported as `LogicAppTemplate.json`  
- VM-Create-Delete-Alert exported as `SentinelAnalyticsRule.json`  
- Screenshots demonstrate end-to-end functionality.  
