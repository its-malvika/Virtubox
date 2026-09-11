# Task Management Application Test Cases

## Inspection status

The workspace was inspected on 2026-09-11. It contains only the assessment screenshot and no application source, package manifest, API documentation, database schema, or test configuration. The cases below are therefore requirement-based and must be mapped to the real routes, fields, and response messages after the application is provided.

## Assumptions and limitations

- Registration uses a unique email or username and a password.
- Login uses the same identifier and password.
- Tasks have at least one required text field, but the exact field name is not known.
- Task records belong to the logged-in user if ownership is implemented by the application.
- HTTP status codes and UI messages are not assumed because no implementation is available.
- No automated test code is included yet: there is no application entry point or test framework against which executable tests can run.

## Test cases

| Test Case ID | Module | Test Scenario | Precondition | Test Steps | Test Data | Expected Result | Type |
|---|---|---|---|---|---|---|---|
| TC-REG-01 | Registration | Register with valid details | Registration page/API is available; user does not exist | Submit all required fields | Unique valid identifier, valid password, matching confirmation if present | Account is created and the user receives the documented success result | Positive |
| TC-REG-02 | Registration | Register with an existing identifier | A user already exists | Submit the same email/username again | Existing identifier and otherwise valid data | Registration is rejected without creating a duplicate account | Negative |
| TC-REG-03 | Registration | Reject invalid identifier format | Registration is available | Submit an invalid email if email format is required | `not-an-email` | Validation error is returned and no account is created | Negative |
| TC-REG-04 | Registration | Reject missing required fields | Registration is available | Leave one required field absent and submit | Missing email, username, or password, one at a time | Validation error identifies the missing field; request does not crash | Negative |
| TC-REG-05 | Registration | Reject empty fields | Registration is available | Submit whitespace or empty values | Empty strings and whitespace-only values | Empty required values are rejected | Edge |
| TC-REG-06 | Registration | Enforce password rules | Password rules are documented by the application | Submit a password below the allowed strength/length | Weak or too-short password | Password is rejected if such validation exists; otherwise record the missing validation | Negative |
| TC-REG-07 | Registration | Reject password confirmation mismatch | Confirmation field exists | Submit different password and confirmation values | `Password1!` and `Password2!` | Registration is rejected with a clear validation error | Negative |
| TC-LOGIN-01 | Login | Login with valid credentials | A valid user exists | Submit correct identifier and password | Registered credentials | User is authenticated and can reach protected functionality | Positive |
| TC-LOGIN-02 | Login | Reject incorrect password | A valid user exists | Submit the correct identifier with a wrong password | Registered identifier and wrong password | Login is rejected without creating an authenticated session | Negative |
| TC-LOGIN-03 | Login | Reject unknown user | No user exists for the identifier | Submit credentials | Unknown identifier and any password | Login is rejected without revealing unnecessary account details | Negative |
| TC-LOGIN-04 | Login | Reject missing or empty credentials | Login is available | Submit with identifier, password, or both omitted/empty | Empty and whitespace-only values | Validation error is returned; application does not crash | Negative |
| TC-LOGIN-05 | Login | Protect task list when unauthenticated | No active session | Request/open the task list directly | No credentials/session | Request is rejected or redirected according to the application contract | Negative |
| TC-CREATE-01 | Tasks | Create a valid task | User is authenticated | Submit a task with all required fields | Normal valid task data | One task is created for the authenticated user and appears in the list | Positive |
| TC-CREATE-02 | Tasks | Reject missing or empty task field | User is authenticated | Omit or empty a required task field | Empty and whitespace-only task value | Task is rejected and no incomplete record is created | Negative |
| TC-CREATE-03 | Tasks | Handle boundary-length task data | User is authenticated; limits are known | Submit values at and beyond each documented limit | Minimum, maximum, and maximum plus one characters | Boundary-valid data is accepted; over-limit data is rejected or handled as documented | Edge |
| TC-CREATE-04 | Tasks | Reject unauthenticated creation | No active session | Submit a create request directly | Valid task data without credentials | Request is rejected and no task is created | Negative |
| TC-READ-01 | Tasks | View the authenticated user's task list | User is authenticated | Open/request the task list | User with existing tasks | Only the user's tasks are returned and displayed | Positive |
| TC-READ-02 | Tasks | Show an empty list for a user with no tasks | Authenticated user has no tasks | Open/request the task list | New user | Empty state is shown; request succeeds without an error | Edge |
| TC-READ-03 | Tasks | Reject unauthenticated list access | No active session | Open/request the task list directly | No credentials | Request is rejected or redirected | Negative |
| TC-UPDATE-01 | Tasks | Update an existing task | User owns an existing task | Change a valid field and submit | Existing task ID and new valid value | Task is updated and the new value is shown | Positive |
| TC-UPDATE-02 | Tasks | Reject invalid update data | User owns an existing task | Submit an empty required field | Existing ID and empty value | Update is rejected and the original task remains unchanged | Negative |
| TC-UPDATE-03 | Tasks | Update a missing task | User is authenticated | Submit an update for a nonexistent or invalid ID | Unknown ID and valid update data | Documented not-found/validation response is returned; no record is created | Negative |
| TC-UPDATE-04 | Tasks | Prevent updating another user's task | Two users exist and ownership is enforced | Authenticate as user B and update user A's task | User A task ID, user B session | Request is rejected and user A's task remains unchanged | Negative |
| TC-DELETE-01 | Tasks | Delete an owned task | User owns an existing task | Delete the task and reload the list | Existing task ID | Delete succeeds and the task no longer appears | Positive |
| TC-DELETE-02 | Tasks | Delete a missing or invalid task | User is authenticated | Delete an unknown or malformed ID | Invalid/nonexistent ID | Stable not-found/validation response is returned; application does not crash | Negative |
| TC-DELETE-03 | Tasks | Prevent unauthenticated or cross-user deletion | No session or a different user's session | Send a delete request | Target task ID without valid ownership | Request is rejected and the task remains | Negative |
| TC-ERROR-01 | Error handling | Handle unexpected server/database failure | A controllable test failure mechanism exists | Trigger the failure while registering or changing a task | Simulated dependency failure | A stable error response/message is returned; sensitive details are not exposed | Edge |

## Automation plan after source is supplied

1. Identify the package manager and existing test command.
2. Map each case to the actual route or UI component and field names.
3. Run against an isolated test database or a documented test fixture.
4. Add setup and cleanup using the project's existing conventions.
5. Keep only cases supported by implemented behavior; record unsupported requirements as gaps.