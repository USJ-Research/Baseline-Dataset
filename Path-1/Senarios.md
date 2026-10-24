# Dataset 1 — Error-Free Scenarios

This document lists every user-interaction scenario the application can produce
when running normally (no injected faults, DB healthy, S3 healthy). All of these
scenarios correspond to `label=0` entries in the logging middleware, since none
of them involve a 404/500/502/503 status or a `CSRF_FAILURE` tag.

## Auth

| Tag | Status | Scenario |
|---|---|---|
| LOGIN_ACCESSED | 200 | Visits login page while logged out |
| LOGIN_FINISHED | 302 | Visits `/login/` while already logged in |
| LOGIN_SUCCESS | 302 | Submits correct username/password |
| INVALID_REQUEST | 401 | Submits wrong username/password |
| AUTH_REDIRECT | 302 | Opens a login-required page while logged out |
| LOGOUT_SUCCESS | 302 | Logs out while logged in |
| LOGOUT_FINISHED | 302 | Hits `/logout/` while already logged out |

## Register

| Tag | Status | Scenario |
|---|---|---|
| REGISTER_ACCESSED | 200 | Visits the registration page |
| REGISTER_SUCCESS | 302 | Submits valid registration details |
| INVALID_REQUEST | 400 | Submits mismatched passwords on registration |

## Rooms

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

## Profile

| Tag | Status | Scenario |
|---|---|---|
| PROFILE_ACCESSED | 200 | Views a profile |
| PROFILEUPDATE_ACCESSED | 200 | Opens own profile-edit page |
| PROFILEUPDATE_SUCCESS | 302 | Updates own profile successfully |
| INVALID_REQUEST | 400 | Submits an empty username while updating profile |
| PROFILEDELETE_ACCESSED | 200 | Opens own account-delete confirm page |
| PROFILEDELETE_SUCCESS | 302 | Deletes own account |
| FORBIDDEN_ACCESS | 403 | Non-owner tries to edit/delete someone else's profile |

## Password Reset

| Tag | Status | Scenario |
|---|---|---|
| PASSWORDRESET_ACCESSED | 302 | Requests a password reset with a valid-format email (+200 for the GET page view) |
| INVALID_REQUEST | 400 | Submits a malformed email on the reset form |
| PASSWORDRESETCONFIRM_ACCESSED | 200 | Opens an invalid/expired reset link |
| INVALID_REQUEST | 400 | Submits a mismatched new password on a valid link |
| PASSWORDRESETCONFIRM_ACCESSED | 302 | Submits a valid matching new password on a real link |
| PASSWORDRESETDONE_ACCESSED | 200 | Lands on the "check your email" page |
| PASSWORDRESETCOMPLETE_ACCESSED | 200 | Lands on the "password changed" confirmation page |

## Read-Only

| Tag | Status | Scenario |
|---|---|---|
| HOME_ACCESSED / HOME_SEARCH | 200 | Visits homepage / searches rooms |
| TOPICVIEW_ACCESSED / TOPICVIEW_SEARCH | 200 | Visits topics page / searches topics |
| ACTIVITYVIEW_ACCESSED | 200 | Visits the activity feed |
