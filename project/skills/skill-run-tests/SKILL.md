---
description: "Run tests and show summary results"
user_invocable: true
---

# Run Tests

Execute the project's test suite and report results.

## Steps

1. **Detect Test Command**: Check for common test configurations:
   - `package.json` -- `npm test` or scripts section
   - `pytest.ini` / `setup.cfg` -- `pytest`
   - `pom.xml` -- `mvn test`
   - `build.gradle` -- `gradle test`
   <!-- CUSTOMIZE: Replace with your project's test command -->

2. **Run Tests**: Execute the detected (or configured) test command.

3. **Report Summary**: Show only the summary:
   - Total tests, passing, failing, pending/skipped
   - If failures: show test names and brief error messages
   - Do NOT dump full test output into the conversation

4. **On Failure**: If tests fail:
   - Show which tests failed and their error messages
   - Offer to investigate with `/skill-debug`

## Tips
- For a specific test file: `/skill-run-tests path/to/test.spec.js`
- For a pattern: `/skill-run-tests -g "user authentication"`
