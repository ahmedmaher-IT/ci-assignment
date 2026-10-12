# Pipeline Design

1. **Triggers:** The pipeline is triggered on a `push` to the `main` branch (excluding Markdown files and files in the `docs/` directory), on pull requests targeting `main`, manually via `workflow_dispatch`, and on a schedule every Monday at 07:00 UTC.
2. **Jobs Graph:**
   - The first job is `test` (runs first independently).
   - The `package` job depends on the success of `test` (`needs: test`).
   - The `verify` job depends on the success of `package` (`needs: package`).
   - The `report` job depends on both `test` and `package` and runs always, regardless of success or failure (`if: always()`).
   - The `pr-comment` job runs only on pull requests after `package` succeeds.
3. **Artifacts:** The pipeline produces two types of artifacts: a JUnit test report (`test-report`) and a compressed application package in `.tar.gz` format (`package`).
4. **Type of Pipeline:** This pipeline represents **Continuous Integration (CI)** because it tests, packages, and verifies code quality automatically upon every change without deploying directly to a production environment (Continuous Deployment).
5. **Behavior on Failure:** If the tests (`test`) fail, the `package` job must be stopped and skipped because we never want to package broken or faulty code.