# Status Code Summary

All HTTP status codes this application can produce, split by whether they
map to a non-error (`label=0`) or error (`label=1`) scenario in the logging
middleware.

## Non-Error Scenarios (label=0)

200   OK                     — request succeeded normally
302   Found                  — redirect (e.g. after login, after a successful action)
400   Bad Request            — invalid client input (empty/malformed form field)
401   Unauthorized           — authentication failed (wrong login credentials)
403   Forbidden              — non-owner tries to edit/delete someone else's content

## Error Scenarios (label=1)

403   Forbidden              — missing/invalid CSRF token
404   Not Found              — no matching URL route...
500   Internal Server Error  — unhandled exception in the view's code
502   Bad Gateway            — upstream dependency (S3) failed
503   Service Unavailable    — server dependency (database) down, or a probe check failing

> Note: `403` appears in both groups with respective label

