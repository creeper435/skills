---
name: create-task
description: Create to-do task item in Jira with curl command. Use when user mentions creating, adding, or making a new task, issue, or to-do item.
---

# Create Task

## Skill Dependency

N/A

## Quick Start

```bash
# Source credentials and execute
source Skills/create-task/.env && curl --request POST \
  --url "https://${JIRA_SUBDOMAIN}.atlassian.net/rest/api/3/issue" \
  --user "${JIRA_USERNAME}:${JIRA_API_TOKEN}" \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/json' \
  --data '{...}'
```

## Security Guideline

- Never log or display credential values
- Never hardcode credentials in commands
- Always read from .env file only
- Mask the sensitive data if display
- Verify sensitive data silently without exposing their values to the user


## API reference**

See [reference.md](reference.md) for all methods

## Workflow

Copy this checklist and check off items as you complete them:

Step 1 - 3 should be pre-loaded

```
Task Progress:
- [ ] Step 1: System Pre-Check
- [ ] Step 2: Credentials Management
- [ ] Step 3: Collect User Info
- [ ] Step 4: Input Validation
- [ ] Step 5: Confirmation Before Execution
- [ ] Step 6: Apply HTTP request
- [ ] Step 7: Verify output
- [ ] Step 8: Display result
```

**Step 1: System Pre-Check**

Verify if curl installed with `curl --version` if not, installed the module below

windows:
```
winget install curl.curl
```

linux:
```
sudo apt update
sudo apt install curl
```

macos:
```
brew install curl
```

**Step 2: Credentials Management**

Read from `.env` file in the skill/source directory. **Never ask the user for these** — they must be pre-configured:

```bash
JIRA_SUBDOMAIN=your-domain
JIRA_USERNAME=your@email.com
JIRA_API_TOKEN=your_api_token
JIRA_ASSIGNEE_ID=your_account_id
JIRA_PROJECT_KEY=KEY
```

Before any interaction, verify ALL credentials exist silently without exposing their values to the user:

```bash
# Silently check if all required variables exist without displaying their values
for var in JIRA_SUBDOMAIN JIRA_USERNAME JIRA_API_TOKEN JIRA_PROJECT_KEY JIRA_ASSIGNEE_ID; do
  grep -q "^${var}=" skills/create-task/source/.env || echo "Missing ${var}"
done
```

If **any** credential is missing → stop immediately and tell the user:

```
❌ Missing Jira credentials. Please configure your .env file first.

Missing fields: [list specific missing fields]

Required format:
JIRA_SUBDOMAIN=your-domain
JIRA_USERNAME=your@email.com
JIRA_API_TOKEN=your_api_token
JIRA_ASSIGNEE_ID=your_account_id
JIRA_PROJECT_KEY=your_project_key

See the .env example at the bottom of this file.
```


**Step 3: Collect User Info**

**Step 3.1: Extract Information from User's Request**

**Analyze the initial request** and extract any provided information:

| Field          | Extraction Hints |
|----------------|------------------|
| summary        | Main subject of request, key nouns/verbs |
| description    | Additional details, context provided |
| label          | Keywords: "work", "personal", "client", customer name mentioned |
| customer_name  | Proper nouns, company names (if work-related) |
| due_date       | Date mentions: "Friday", "next week", "April 10", "tomorrow" |

Example extractions:
- "Create a work task for Acme to update SSL certs by April 15"
  - label: work ✓
  - customer_name: Acme ✓
  - summary: update SSL certs ✓
  - due_date: 2026-04-15 ✓
  - Missing: description

**Step 3.2: Intelligent Questioning**

Only ask for **missing required fields**. Use this priority order:

1. **Label** (if not extracted)
   - Ask: "Is this a **work** or **personal** task?"

2. **Customer Name** (if label=work)
   - Validation: Must be 2+ characters
   - If empty: Re-ask, explain it's required for work tasks

3. **Summary** (if not extracted)
   - Validation: Must be 3-100 characters
   - If empty/too short: Re-ask with example

4. **Description** (if not extracted)
   - Validation: Must be 10+ characters
   - If empty/too short: Re-ask with guidance

5. **Due Date** (if not extracted)
   - Accept natural language: "tomorrow", "next Monday", "April 15"
   - Convert to YYYY-MM-DD format before submission

**Step 3.3: Acknowledge Extracted Information**

If you extracted information, acknowledge it:
```
I found these details from your request:
- Label: work
- Customer: Acme Corp
- Summary: Update SSL certificates
- Due date: 2026-04-15

Now I just need: [list missing fields]
```

**Step 4: Input Validation**

Validate user response before confirmation:

### Summary Validation
- **Length**: 3-100 characters
- **Content**: Must be descriptive (not just "task" or "todo")
- **Bad**: "task", "do something", "x"
- **Good**: "Deploy nginx to production", "Review Q2 budget"

### Description Validation
- **Length**: 10+ characters (aim for meaningful detail)
- **Content**: Should explain what needs to be done and why
- **Bad**: "do it", "task"
- **Good**: "Update nginx.conf with new SSL certificates and reload the service without downtime"

### Label Validation
- **Valid values**: ONLY "work" or "personal"
- **Case-insensitive**: Accept "Work", "PERSONAL", etc. → normalize to lowercase
- **Invalid handling**: If user provides anything else, re-ask with explicit choices

### Customer Name Validation (only if label=work)
- **Length**: 2+ characters
- **Required**: Cannot be empty for work tasks
- **Format**: Free text, but should be a name/company

### Due Date Validation & Conversion
- **Accepted formats**:
  - ISO format: YYYY-MM-DD (e.g., 2026-04-10)
  - Natural language: "tomorrow", "next Friday", "in 3 days"
  - Date strings: "April 10", "10 April 2026"
  
- **Conversion rules**:
  - Use current date for context: {current_date}
  - "tomorrow" → {current_date + 1 day}
  - "next Friday" → {next occurring Friday}
  - "April 10" → assume current year if not specified
  
- **Output format**: Always convert to YYYY-MM-DD
- **Validation**: Date must be today or future (warn if past date)

**Step 5: Confirmation Before Execution**

Confirm with user with following rich text:

```
📋 Ready to create this task:

Project      : ${project_key}
Type         : Task
Label        : ${label}
${customer_name ? "Customer     : " + customer_name : ""}
Summary      : ${label === 'work' && customer_name ? customer_name + " - " : ""}${summary}
Description  : ${description}
Due Date     : ${due_date}
Assignee     : You (from .env)

Does this look correct? (yes/no)
```

- **If yes/y/correct/looks good**: Proceed to API execution
- **If no/n/wait**: Ask what needs to be changed
  - Allow editing any field
  - Re-confirm after changes
- **If user wants to cancel**: Acknowledge and stop
- **No response**: Do not assume, ask again


**Step 6: API Execution**

Load Environment Variable

```bash
# Source the .env file
source skills/create-task/source/.env
```

Update the variables as per collected from user conversation, and execute the `curl` command as outlined in `reference.md`.

**Step 7: Verify output**

Expected Success Response (HTTP 201):

```json
{
  "id": "10000",
  "key": "CWS-5",
  "self": "https://your-domain.atlassian.net/rest/api/3/issue/10000"
}
```

**Extract needed fields:**
- `key`: The issue identifier (e.g., CWS-5)
- Use `key` to construct URL: `https://${JIRA_SUBDOMAIN}.atlassian.net/browse/${key}`



**Step 8: Response Handling**

Success Response (HTTP 201)

Display rich success message:

```
✅ Jira Task Created Successfully!

┌────────────────────────────────────────────
│ Key         : ${key}
│ Summary     : ${full_summary}
│ Due Date    : ${due_date}
│ Label       : ${label}
└────────────────────────────────────────────

🔗 Quick link: https://${JIRA_SUBDOMAIN}.atlassian.net/browse/${key}
```

If it is not Success Response, Stop the retry mechanism.

Error Response: Please reference reference.md and output corresponding error message to user.

```
❌ Jira Task Fail to Deleted Successfully!
┌────────────────────────────────────────────
│ Error: Provide the Error Message Here
└────────────────────────────────────────────
```