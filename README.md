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
- **Collaboration:** Microsoft Outlook, Microsoft Teams  

---

## Workflow Diagram
VM event → Sentinel alert → Incident → Logic App → LLM → Teams / Email 

## Screenshots
- Sentinel Analytics Rule:
<img width="469" height="738" alt="Screenshot 2025-09-10 at 10 27 08 PM" src="https://github.com/user-attachments/assets/f8db6c28-6f57-454a-a4bf-b16e0be113b0" />
  
- Logic App Designer:
<img width="670" height="773" alt="Screenshot 2025-09-10 at 10 39 57 PM" src="https://github.com/user-attachments/assets/13f1698e-8614-4267-ad1c-28104ad9de96" />

- Email Output:
<img width="964" height="302" alt="Screenshot 2025-09-10 at 10 25 23 PM" src="https://github.com/user-attachments/assets/fb1a751d-cc8e-4174-8e15-37fd7ec0fde1" />
  

---

## Code Snippets
### 1. Sentinel Analytics Rule KQL
```kql AzureActivity
| where OperationNameValue in ("MICROSOFT.COMPUTE/VIRTUALMACHINES/WRITE", "MICROSOFT.COMPUTE/VIRTUALMACHINES/DELETE")
      and ActivityStatusValue == "Start"
| project
    TimeGenerated,
    OperationNameValue,
    ActivityStatusValue,
    VM_Name = Properties_d.resource,
    ResourceGroup,
    SubscriptionId,
    Initiated_By = Caller
| order by TimeGenerated desc
```
### 2. Logic App Trigger (Incident Trigger)
```
{
  "type": "ApiConnectionWebhook",
  "inputs": {
    "host": {
      "connection": { "name": "@parameters('$connections')['azuresentinel']['connectionId']" }
    },
    "body": { "callback_url": "@listCallbackUrl()" },
    "path": "/incident-creation"
  }
}
```
### 3. Logic App Actions

### a) Get Incident Details
```
{
  "type": "ApiConnection",
  "inputs": {
    "host": { "connection": { "name": "@parameters('$connections')['azuresentinel']['connectionId']" } },
    "method": "get",
    "path": "/Incidents/subscriptions/@{encodeURIComponent('3f173bcc-60aa-4db0-9dd1-ff7ce83a45c8')}/resourceGroups/@{encodeURIComponent('SIEM-Lab')}/workspaces/@{encodeURIComponent('d0a3af8a-abda-45ce-93ab-330188610950')}/alerts/@{encodeURIComponent('16a47000-1fd3-4ccb-8389-50fcf805287c')}"
  }
}
```

### b) Compose Email Body
```
{
  "type": "Compose",
  "inputs": "Incident '@{triggerOutputs()?['body/Title']}' of severity @{triggerOutputs()?['body/Severity']} triggered by @{triggerOutputs()?['body/Caller']} on @{triggerOutputs()?['body/Resource']}."
}
```

### c) Send Email (V2)
```
{
  "type": "ApiConnection",
  "inputs": {
    "host": { "connection": { "name": "@parameters('$connections')['outlook']['connectionId']" } },
    "method": "post",
    "body": {
      "To": "techwithcam@outlook.com",
      "Subject": "Sentinel Incident Summary",
      "Body": "<p>@{outputs('Compose')}</p>",
      "Importance": "High"
    },
    "path": "/v2/Mail"
  }
}
```
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
