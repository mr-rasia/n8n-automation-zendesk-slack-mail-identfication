# n8n-automation-zendesk-slack-mail-identfication
this is the repo for the cloud keeper and in which we are going to create a system and that system is going to distinguish between a normal email and an email that have ticket description in it
# Shift Owner Scheduler (n8n Workflow)

This project is an automated **Shift Owner Notification System** built using n8n, Slack, and Google Sheets.

It sends a **daily Slack notification at 8 PM (IST)** for the **next day's shift owner**, with an approval system.

---

## Features

* Runs daily at **8 PM IST**
* Fetches **tomorrow’s shift details**
* Skips weekends automatically
* Reads data from Google Sheets
* Sends Slack message with:

  * Shift Owner
  * On-call support
* Approval-based workflow
* Conditional flow for approval and rejection

---

## Workflow Overview

```
Schedule Trigger (8 PM)
        ↓
Google Sheets (Fetch Data)
        ↓
Code Node (Filter Tomorrow's Shift)
        ↓
IF (Weekend Check)
        ↓
Slack Approval Message
        ↓
IF Approved?
   ↙           ↘
Yes             No
↓               ↓
Notify Success   Handle Rejection
```

---

## Google Sheets Structure

Your sheet must follow this format exactly:

| Date | Month | 9:00AM - 6:00PM | on call support |
| ---- | ----- | --------------- | --------------- |
| 1    | May   | user1           | user2           |

Notes:

* `Date` should be a number (e.g., 1, 2, 3)
* `Month` should be full name (e.g., May, June)
* Column names must match exactly (including spaces)

---

## Setup Instructions

### 1. Import Workflow

1. Open n8n
2. Click **Import Workflow**
3. Paste the JSON file
4. Save

---

### 2. Configure Google Sheets

* Add your Google Sheets credentials
* Select your spreadsheet
* Ensure:

  * Sheet name is correct
  * Columns match required format

---

### 3. Configure Slack

* Add Slack credentials in n8n
* Replace:

  ```
  your_slack_channel_id
  ```

  with your actual channel ID

To get the channel ID:

* Open Slack
* Click the channel
* Open details panel
* Copy the Channel ID

---

### 4. Enable Approval Flow

The workflow uses the `sendAndWait` Slack node.

Requirements:

* Slack App with interactivity enabled
* Required permissions such as `chat:write`, `commands`

---

### 5. Activate Workflow

* Click **Activate**
* The workflow will run automatically every day at 8 PM

---

## How It Works

* Converts current time to IST
* Moves to the next day
* Matches:

  * `Date`
  * `Month`
* Extracts:

  * Shift Owner
  * On-call Support
* Sends Slack message
* Waits for approval
* Routes based on response

---

## Common Issues

### No data found

Check:

* Date format (number vs string)
* Month spelling
* Extra spaces in column names

---

### Slack not responding

Ensure:

* Slack credentials are connected
* App has proper permissions
* Interactivity is enabled

---

### Workflow runs but no message

Check:

* IF node logic
* Weekend condition may be triggered

---

## Customization

You can modify:

* Trigger time by changing `triggerAtHour`
* Shift timing by updating column name
* Add multiple shifts by extending the code node
* Add escalation by extending Slack nodes

---

## Example Output

```
Shift Details (Tomorrow)

Date: 2 may
9:00AM - 6:00PM: @user1
On Call Support: @user2
```

---

## Contributing

You can extend this project with:

* Multi-shift support
* UI-based approvals
* Dashboard integrations

---

## License

MIT License

---

## Author

Built to simplify shift scheduling using automation.

---

For advanced use cases such as multi-team scheduling, escalation handling, auto-updating sheets, or Slack form integration, this workflow can be extended further.


# Gmail → Slack → Zendesk Automation (n8n)

This project is an automated cloud support ticketing workflow built using n8n, integrating Gmail, AI analysis, Slack approval, and Zendesk ticket creation.

It monitors incoming emails, detects potential cloud incidents using AI, and creates Zendesk tickets after Slack approval.

---

## Overview

This workflow performs the following steps:

1. Continuously monitors Gmail inbox
2. Uses AI to analyze incoming emails
3. Detects if the email qualifies as a cloud support incident
4. Sends a Slack approval request
5. Creates a Zendesk ticket if approved
6. Sends confirmation notification to Slack

---

## Architecture Flow

```
Gmail Trigger
   ↓
AI Agent (Email Analysis)
   ↓
Structured Output Parser
   ↓
IF (ticket && cc_support)
   ↓
Slack Approval (sendAndWait)
   ↓
IF (approved)
   ↓
Zendesk Ticket Creation
   ↓
Slack Notification
```

---

## Features

* Automated email monitoring (runs every minute)
* AI-based classification of cloud incidents
* Detects:

  * Infrastructure issues
  * Outages
  * Billing problems
  * Urgency signals
* Extracts sender email intelligently
* Slack-based approval system
* Zendesk ticket auto-creation
* End-to-end automation with minimal manual effort

---

## Node Details

### 1. Gmail Trigger

* Polls inbox every minute
* Captures:

  * Subject
  * From
  * Email snippet

---

### 2. AI Agent

* Uses LLM to analyze email content
* Determines:

  * ticket (true/false)
  * cc_support (true/false)
  * sender
  * ticket_summary

---

### 3. Structured Output Parser

Ensures output follows strict JSON format:

```json
{
  "ticket": boolean,
  "cc_support": boolean,
  "sender": string,
  "ticket_summary": string
}
```

---

### 4. IF Node (Condition Check)

Checks:

* ticket == true
* cc_support == true

Only proceeds if both are true.

---

### 5. Slack Approval (sendAndWait)

Sends message:

```
Cloud Ticket Decision
Ticket: true/false
CC: true/false
Summary: <AI Generated Summary>
```

Waits for approval response.

---

### 6. IF Node (Approval Check)

Condition:

```
approved == true
```

---

### 7. Zendesk Ticket Creation

Creates a ticket with:

* Description includes:

  * Ticket status flags
  * Reporter email
  * Incident summary
  * Auto-generated message

---

### 8. Slack Notification

Sends confirmation:

```
Ticket created in Zendesk
```

---

## Setup Instructions

### 1. Import Workflow

* Open n8n
* Go to Workflows
* Click "Import from JSON"
* Paste the provided JSON

---

### 2. Configure Credentials

You must configure:

* Gmail OAuth
* Slack API
* Zendesk API
* Groq / LLM API

---

### 3. Update Nodes

* Gmail Trigger → select account
* Slack nodes → select workspace + channel
* Zendesk node → configure domain + API token
* AI Model → ensure API key is set

---

### 4. Activate Workflow

* Toggle workflow to Active
* Ensure polling is working

---

## Use Cases

* Cloud support automation
* Incident detection system
* DevOps alert pipeline
* IT support triage automation
* Email-to-ticket conversion system

---

## Important Notes

* Ensure Slack approval step has proper permissions
* Zendesk API access must be enabled
* AI output must strictly match schema (or workflow may fail)
* Gmail polling frequency can be adjusted if needed

---

## Future Improvements

* Add priority tagging (P1, P2, etc.)
* Auto-assign tickets in Zendesk
* Add logging (Google Sheets / DB)
* Integrate monitoring tools (AWS, GCP alerts)
* Add retry/failure handling

---

## Status

Inactive by default ("active": false).
Enable manually after setup.

---

