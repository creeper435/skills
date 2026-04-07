# API Reference

## Contents
- Authentication and setup
- Core methods (create, read, update, delete)
- Error handling patterns
- Code examples

## Authentication and setup

Use Basic Auth for API authentication.

## Core methods

This skill only accept **POST** HTTP Request

## Error handling patterns

- 400 Bad Request: 
    - is missing required fields.
    - contains invalid field values.
    - contains fields that cannot be set for the issue type.
    - is by a user who does not have the necessary permission.
    - is to create a subtype in a project different that of the parent issue.
    - is for a subtask when the option to create subtasks is disabled.
    - is invalid for any other reason.
    
- 401 Unauthorized:
    - Returned if the authentication credentials are incorrect or missing.

- 403 Forbidden:
    - Returned if the user does not have the necessary permission.

- 422 Unprocessable Entity:
    - Returned if a configuration problem prevents the creation of the issue.

## Code examples

POST /rest/api/3/issue
```bash
// This sample uses Atlassian Forge
// https://developer.atlassian.com/platform/forge/

curl --silent --show-error --write-out '\nHTTP_CODE:%{http_code}\n' --request POST \
  --url "https://${JIRA_SUBDOMAIN}.atlassian.net/rest/api/3/issue" \
  --user "${JIRA_USERNAME}:${JIRA_API_TOKEN}" \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/json' \
  --data '{
        "fields": {
            "project": {
                "key": "${JIRA_PROJECT_KEY}"
            },
            "issuetype": {
                "name": "Task"
            },
            "summary": "${customer_name} - ${summary}",
            "description": {
                "version": 1,
                "type": "doc",
                "content": [
                    {
                        "type": "paragraph",
                        "content": [
                            {
                                "type": "text",
                                "text": "${description}"
                            }
                        ]
                    }
                ]
            },
            "assignee": {
                "id": "${JIRA_ASSIGNEE_ID}"
            },
            "labels": [
                "${label}"
            ],
            "duedate": "${due_date}"
        }
    }'
```
