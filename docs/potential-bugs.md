# Potential Bugs / Risk Areas

## Review status

No application source was available for static review. The workspace contains only the assessment screenshot. The items below are **potential risks requiring verification**, not confirmed bugs. They are included to show the review checklist that should be applied once the source and configuration are available.

### BUG-01 - Passwords may be stored or logged insecurely

**Description:** Verify that passwords are hashed with a suitable password-hashing function and are absent from logs, responses, and error messages.

**Severity:** Critical if confirmed

**Reason / Impact:** Plaintext or reversible password storage could expose every account after a database or log compromise.

**Related Area:** Registration, authentication service, database model, logging

### BUG-02 - Task endpoints may lack ownership authorization

**Description:** Verify that read, update, and delete operations restrict a task to its owning user rather than trusting only a task ID.

**Severity:** Critical if confirmed

**Reason / Impact:** An authenticated user could view, change, or delete another user's tasks by changing an ID.

**Related Area:** Task CRUD authorization and database queries

### BUG-03 - Protected routes may accept missing or invalid sessions

**Description:** Verify that every task endpoint checks authentication server-side, including direct API requests that bypass the UI.

**Severity:** Critical if confirmed

**Reason / Impact:** Missing middleware or inconsistent checks could allow unauthenticated task access or modification.

**Related Area:** Authentication middleware and task routes

### BUG-04 - Duplicate registration may be race-prone

**Description:** Verify that the identifier has a database-level uniqueness constraint in addition to an application-level duplicate check.

**Severity:** Major if confirmed

**Reason / Impact:** Concurrent registration requests could create duplicate accounts or produce an unhandled database error.

**Related Area:** User model, registration transaction, database migrations

### BUG-05 - Invalid task IDs may cause an unhandled exception

**Description:** Verify that malformed IDs and nonexistent IDs are validated before database lookup and converted to stable client errors.

**Severity:** Major if confirmed

**Reason / Impact:** A malformed request could return a server error or crash a request handler instead of a controlled validation/not-found response.

**Related Area:** Task route parameter parsing and error handling

### BUG-06 - Server-side validation may be incomplete

**Description:** Verify required fields, whitespace-only values, length limits, and allowed formats on the server rather than only in the client.

**Severity:** Major if confirmed

**Reason / Impact:** Direct requests could save incomplete or unexpectedly large data, causing inconsistent records and downstream failures.

**Related Area:** Request schemas, create/update handlers, database constraints

### BUG-07 - Database failures may leak implementation details

**Description:** Verify that database exceptions are handled centrally and that responses do not expose SQL, stack traces, credentials, or schema details.

**Severity:** Major if confirmed

**Reason / Impact:** Detailed errors aid attackers and make client behavior inconsistent during outages.

**Related Area:** Persistence layer and API error middleware

### BUG-08 - Authentication expiry may not be enforced consistently

**Description:** Verify token/session expiry, logout invalidation where applicable, and the behavior of task requests made after expiry.

**Severity:** Major if confirmed

**Reason / Impact:** Stale credentials could continue to access protected data longer than intended, or expired sessions could cause confusing partial failures.

**Related Area:** Session/token configuration and auth middleware

### BUG-09 - Delete/update responses may not match database state

**Description:** Verify that a successful response is sent only after the database operation succeeds and that the UI refreshes or removes the affected task correctly.

**Severity:** Major if confirmed

**Reason / Impact:** Users may believe data was saved or deleted when it was not, creating data-consistency problems.

**Related Area:** Task handlers, frontend API response handling, list refresh logic

### BUG-10 - Authentication error messages may reveal account existence

**Description:** Verify whether login errors distinguish an unknown account from a wrong password and whether that distinction is necessary.

**Severity:** Minor if confirmed

**Reason / Impact:** Account enumeration can help attackers identify valid users; a generic authentication failure is usually safer.

**Related Area:** Login handler and client error display

## Required next step

Provide the application source or open the correct project folder. The confirmed bug list must then be rewritten from actual file and route evidence, and the executable tests must be added using the discovered framework and isolated database setup.