# Application Log Scenarios

This document lists every scenario the application's logging middleware can
produce, split into `label=0` (error-free) and `label=1` (genuine fault:
404/500/502/503 status, or a `CSRF_FAILURE` tag).

---

## Status Code Summary

### Non-Error Scenarios (label=0)

| Status | Meaning |
|---|---|
| 200 OK | request succeeded normally |
| 302 Found | redirect (e.g. after login, after a successful action) |
| 400 Bad Request | invalid client input (empty/malformed form field) |
| 401 Unauthorized | authentication failed (wrong login credentials) |
| 403 Forbidden | non-owner tries to edit/delete someone else's content |

### Error Scenarios (label=1)

| Status | Meaning |
|---|---|
| 403 Forbidden | missing/invalid CSRF token |
| 404 Not Found | no matching URL route |
| 500 Internal Server Error | unhandled exception in the view's code |
| 502 Bad Gateway | upstream dependency (S3) failed |
| 503 Service Unavailable | server dependency (database) down, or a probe check failing |

> Note: `403` appears in both groups with its respective label.

---

## Dataset 1 - Error-Free Scenarios

### Auth

| Tag | Status | Scenario |
|---|---|---|
| LOGIN_ACCESSED | 200 | Visits login page while logged out |
| LOGIN_FINISHED | 302 | Visits `/login/` while already logged in |
| LOGIN_SUCCESS | 302 | Submits correct username/password |
| INVALID_REQUEST | 401 | Submits wrong username/password |
| AUTH_REDIRECT | 302 | Opens a login-required page while logged out |
| LOGOUT_SUCCESS | 302 | Logs out while logged in |
| LOGOUT_FINISHED | 302 | Hits `/logout/` while already logged out |

### Register

| Tag | Status | Scenario |
|---|---|---|
| REGISTER_ACCESSED | 200 | Visits the registration page |
| REGISTER_SUCCESS | 302 | Submits valid registration details |
| INVALID_REQUEST | 400 | Submits mismatched passwords on registration |

### Rooms

| Tag | Status | Scenario |
|---|---|---|
| CREATEROOM_ACCESSED | 200 | Visits the "create room" page |
| CREATEROOM_SUCCESS | 302 | Creates a room with valid details |
| INVALID_REQUEST | 400 | Submits an empty room name |
| ROOM_ACCESSED | 200 | Opens a room |
| ROOM_SUCCESS | 302 | Sends a message successfully |
| INVALID_REQUEST | 400 | Tries to send an empty message |
| UPDATEROOM_ACCESSED | 200 | Owner opens the edit-room form |
| UPDATEROOM_SUCCESS | 302 | Owner edits their room successfully |
| INVALID_REQUEST | 400 | Owner submits an empty name while editing |
| DELETEROOM_ACCESSED | 200 | Owner opens the delete-room confirm page |
| DELETEROOM_SUCCESS | 302 | Owner deletes their room |
| DELETEMESSAGE_ACCESSED | 200 | Owner opens the delete-message confirm page |
| DELETEMESSAGE_SUCCESS | 302 | Owner deletes their own message |
| FORBIDDEN_ACCESS | 403 | Non-owner tries to edit/delete someone else's room or message |

### Profile

| Tag | Status | Scenario |
|---|---|---|
| PROFILE_ACCESSED | 200 | Views a profile |
| PROFILEUPDATE_ACCESSED | 200 | Opens own profile-edit page |
| PROFILEUPDATE_SUCCESS | 302 | Updates own profile successfully |
| INVALID_REQUEST | 400 | Submits an empty username while updating profile |
| PROFILEDELETE_ACCESSED | 200 | Opens own account-delete confirm page |
| PROFILEDELETE_SUCCESS | 302 | Deletes own account |
| FORBIDDEN_ACCESS | 403 | Non-owner tries to edit/delete someone else's profile |

### Password Reset

| Tag | Status | Scenario |
|---|---|---|
| PASSWORDRESET_ACCESSED | 302 | Requests a password reset with a valid-format email (+200 for the GET page view) |
| INVALID_REQUEST | 400 | Submits a malformed email on the reset form |
| PASSWORDRESETCONFIRM_ACCESSED | 200 | Opens an invalid/expired reset link |
| INVALID_REQUEST | 400 | Submits a mismatched new password on a valid link |
| PASSWORDRESETCONFIRM_ACCESSED | 302 | Submits a valid matching new password on a real link |
| PASSWORDRESETDONE_ACCESSED | 200 | Lands on the "check your email" page |
| PASSWORDRESETCOMPLETE_ACCESSED | 200 | Lands on the "password changed" confirmation page |

### Read-Only

| Tag | Status | Scenario |
|---|---|---|
| HOME_ACCESSED / HOME_SEARCH | 200 | Visits homepage / searches rooms |
| TOPICVIEW_ACCESSED / TOPICVIEW_SEARCH | 200 | Visits topics page / searches topics |
| ACTIVITYVIEW_ACCESSED | 200 | Visits the activity feed |

---

## Dataset 2 - Error Scenarios

### Security / Routing

| Tag | Status | Scenario |
|---|---|---|
| CSRF_FAILURE | 403 | form submitted with missing/invalid CSRF token |
| NOT_FOUND | 404 | visits a URL with no matching route |

### Auth

| Tag | Status | Scenario |
|---|---|---|
| LOGIN_FAILED | 503 / 500 | login attempt fails - DB down / unhandled exception |
| LOGOUT_FAILED | 503 / 500 | logout fails - DB down / unhandled exception |

### Register

| Tag | Status | Scenario |
|---|---|---|
| REGISTER_FAILED | 503 / 500 | registration fails - DB down / unhandled exception |

### Rooms

| Tag | Status | Scenario |
|---|---|---|
| ROOM_FAILED | 503 / 500 | opening/posting in a room fails - DB down / unhandled exception |
| CREATEROOM_FAILED | 503 / 500 | room creation fails - DB down / unhandled exception |
| UPDATEROOM_FAILED | 503 / 500 | room edit fails - DB down / unhandled exception |
| DELETEROOM_FAILED | 503 / 500 | room deletion fails - DB down / unhandled exception |
| DELETEMESSAGE_FAILED | 503 / 500 | message deletion fails - DB down / unhandled exception |

### Profile

| Tag | Status | Scenario |
|---|---|---|
| PROFILE_FAILED | 503 / 500 | profile view fails - DB down / unhandled exception |
| PROFILEUPDATE_FAILED | 503 / 500 / 502 | profile update fails - DB down / unhandled exception / S3 avatar upload fails |
| PROFILEDELETE_FAILED | 503 / 500 | account deletion fails - DB down / unhandled exception |

### Password Reset

| Tag | Status | Scenario |
|---|---|---|
| PASSWORDRESET_FAILED | 503 / 500 | reset request fails - DB down / unhandled exception |
| PASSWORDRESET_EMAIL_FAILED | 500 | reset email fails to send (SMTP error) |
| PASSWORDRESETCONFIRM_FAILED | 503 / 500 | reset confirmation fails - DB down / unhandled exception |

### Read-Only

| Tag | Status | Scenario |
|---|---|---|
| HOME_FAILED | 503 / 500 | homepage load fails - DB down / unhandled exception |
| TOPICVIEW_FAILED | 503 / 500 | topics page fails - DB down / unhandled exception |
| ACTIVITYVIEW_FAILED | 503 / 500 | activity feed fails - DB down / unhandled exception |

### Health Probes

| Tag | Status | Scenario |
|---|---|---|
| HEALTH_FAILED | 503 | /health/ probe finds DB unreachable |
| START_FAILED | 503 | /start/ probe fails on startup |
| READY_FAILED | 503 | /ready/ probe finds DB unreachable |
| LIVE_FAILED | 503 | /live/ probe finds DB unreachable |

> **Note:** `HEALTH_FAILED`, `START_FAILED`, `READY_FAILED`, and `LIVE_FAILED` are generated by the app but dropped by the Promtail pipeline before reaching Loki - they never appear in the actual log dataset. `HEALTH_FAILED` is additionally dead code, since `/health/` isn't wired to any k8s probe.

### Startup / Deployment

| Tag | Status | Scenario |
|---|---|---|
| DB_DOWN | 503 | `migrate`/`makemigrations` fails at container startup (fresh deploy, or a restart triggered by a failed liveness probe) because the DB is unreachable |
