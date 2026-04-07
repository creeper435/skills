---
name: delete-task
description: Delete a task, issue, or to-do item in Jira with curl command. Use when user mentions deleting, removing, or trashing an existing task, issue, or to-do item.
---

# Delete Task

## Skill Dependency

N/A

## Quick Start

```bash
# Source credentials and execute
source skills/delete-task/source/.env && curl --request DELETE \
  --url "https://${JIRA_SUBDOMAIN}.atlassian.net/rest/api/3/issue/${ISSUE_KEY}" \
  --user "${JIRA_USERNAME}:${JIRA_API_TOKEN}"
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
- [ ] Step 6: API Execution
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
```

Before any interaction, verify ALL credentials exist silently without exposing their values to the user:

```bash
# Silently check if all required variables exist without displaying their values
for var in JIRA_SUBDOMAIN JIRA_USERNAME JIRA_API_TOKEN; do
  grep -q "^${var}=" skills/delete-task/source/.env || echo "Missing ${var}"
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

See the .env example at the bottom of this file.
```


**Step 3: Collect User Info**

**Step 3.1: Extract Information from User's Request**

**Analyze the initial request** and extract the issue key:

| Field          | Extraction Hints |
|----------------|------------------|
| issue_key      | Format like PROJ-123 or ABC-45 |

Example extractions:
- "Delete task CWS-5"
  - issue_key: CWS-5 ✓

**Step 3.2: Intelligent Questioning**

Only ask for **missing required fields**. Priority order:

1. **Issue Key** (if not extracted)
   - Ask: "What is the Jira issue Key of the task you want to delete (e.g., CWS-5)?"

**Step 3.3: Acknowledge Extracted Information**

If you extracted information, acknowledge it:
```
I found the issue key from your request:
- Issue Key: CWS-5

Now I just need: [list missing fields if any]
```

**Step 4: Input Validation**

Validate user response before confirmation:

### Issue Key Validation
- **Format**: Project Key (letters) - Issue Number (digits)
- **Bad**: "my task", "123"
- **Good**: "PROJ-123", "CWS-5"

**Step 5: Confirmation Before Execution**

Confirm with user with following rich text:

```
⚠️ Ready to delete this task:

Issue Key    : ${issue_key}

Are you sure you want to permanently delete this issue? This action cannot be undone. (yes/no)
```

- **If yes/y/correct/sure**: Proceed to API execution
- **If no/n/wait/cancel**: Acknowledge and stop
- **No response**: Do not assume, ask again


**Step 6: API Execution**

Load Environment Variable

```bash
# Source the .env file
source skills/delete-task/source/.env
```

Execute the `curl` command as outlined in `reference.md` using the collected issue key:

```bash
ISSUE_KEY="YOUR_COLLECTED_KEY"
curl --silent --show-error --write-out '\nHTTP_CODE:%{http_code}\n' --request DELETE \
  --url "https://${JIRA_SUBDOMAIN}.atlassian.net/rest/api/3/issue/${ISSUE_KEY}" \
  --user "${JIRA_USERNAME}:${JIRA_API_TOKEN}"
```

**Step 7: Verify output**

Expected Success Response (HTTP 204 No Content). There is no response body on a successful delete.
The curl output should have `HTTP_CODE:204` at the end.


**Step 8: Response Handling**

Success Response (HTTP 204)

Display rich success message:

```
✅ Jira Task Deleted Successfully!

┌────────────────────────────────────────────
│ Key         : ${issue_key}
│ Status      : Deleted
└────────────────────────────────────────────
```

If it is not Success Response, Stop the retry mechanism.

Error Response: Please reference reference.md and output corresponding error message to user.

```
❌ Jira Task Fail to Deleted Successfully!
┌────────────────────────────────────────────
│ Error: Provide the Error Message Here
└────────────────────────────────────────────
```