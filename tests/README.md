# Automated tests unavailable

The workspace currently contains no application source, package manifest, routes, database models, or test framework. Executable registration, login, and task CRUD tests cannot be written responsibly without inventing APIs and behavior.

When the application is added, use the test cases in `docs/test-cases.md` to map tests to the real endpoints or UI components. First identify the existing test command and database setup, then add a small suite using the project's conventions and keep test data isolated from production data.