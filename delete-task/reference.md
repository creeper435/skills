# API Reference

## Contents
- Authentication and setup
- Core methods (delete)
- Error handling patterns
- Code examples

## Authentication and setup

Use Basic Auth for API authentication.

## Core methods

This skill only accept **DELETE** HTTP Request

## Error handling patterns

- 400 Bad Request: 
    - Returned if the issue has subtasks and deleteSubtasks is not set to true.
    
- 401 Unauthorized:
    - Returned if the authentication credentials are incorrect.

- 403 Forbidden:
    - Returned if the user does not have permission to delete the issue.

- 404 Not Found:
    - Returned if the issue is not found or the user does not have permission to view the issue.

## Code examples

DELETE /rest/api/3/issue/{issueIdOrKey}
```bash
curl --silent --show-error --write-out '\nHTTP_CODE:%{http_code}\n' --request DELETE \
  --url "https://${JIRA_SUBDOMAIN}.atlassian.net/rest/api/3/issue/${ISSUE_KEY}" \
  --user "${JIRA_USERNAME}:${JIRA_API_TOKEN}"
```
