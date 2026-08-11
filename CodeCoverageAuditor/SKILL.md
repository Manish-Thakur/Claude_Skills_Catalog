---
name: code-coverage-auditor
description: 'Use when: auditing code coverage for APIs, services, controllers, handlers, UI components, libraries, or workflows; finding coverage gaps; scoring before/after coverage; preventing duplicate tests; deciding unit vs integration/E2E placement; and suggesting tests to reach 100% meaningful coverage in any repository.'
argument-hint: 'Target feature, endpoint, component, service, test project, coverage report, or changed files to audit'
---

# Code Coverage Auditor

Use this skill to assess and improve meaningful test coverage in any codebase. It produces a repeatable coverage scorecard, identifies coverage gaps, prevents duplicate tests, and recommends whether each scenario belongs in unit tests, integration tests, E2E tests, or should be merged/removed as duplicate coverage.

## Outcomes

- Feature-level, component-level, endpoint-level, or service-level coverage score before changes.
- Coverage gap index by behavior, input validation, branching logic, error handling, response/output mapping, state changes, and integration/E2E scenarios.
- Duplicate-test check against existing tests, scenario files, snapshots, test summaries, and coverage reports.
- Clear recommendation for each scenario: keep as unit test, keep as integration/E2E test, keep in multiple layers with different assertions, or remove/merge as duplicate.
- Suggestions required to reach 100% meaningful coverage, or the highest realistic target when 100% is blocked.
- After-change coverage score and delta when tests are updated.
- Optional persistent scorecard in an existing test summary, coverage report, PR note, or a new audit report file when requested.

## Required Inputs

Accept any of these inputs:

- Feature, endpoint, route, component, class, module, service, handler, job, command, or workflow name.
- Changed source files or test files.
- Test project, test folder, package, workspace, or repository path.
- Coverage report path, if one exists.
- PR or review scope.

If the scope cannot be inferred, ask for the target feature/component and whether the task is audit-only or should implement test improvements.

## Discovery Procedure

1. Identify the target production code and its responsibilities.
   - Examples: API endpoint, controller action, service method, command handler, repository method, UI component, job, CLI command, library function, mapper, validator, or workflow.
2. Identify the language, test framework, project layout, and existing test conventions.
   - Look for README files, test projects, package scripts, build files, CI configuration, coverage settings, and repository instructions.
3. Locate existing coverage artifacts:
   - Unit tests, integration tests, E2E tests, feature/spec files, snapshots, golden files, fixture data, and test summaries.
   - Coverage reports such as Cobertura, JaCoCo, Istanbul/nyc, lcov, OpenCover, Coverlet, coverage.py, gcov, or vendor-specific reports.
4. Read the target production code and related helpers, mappers, validators, services, repositories, and types.
5. Read all matching tests before proposing or writing new tests.
6. If implementation is requested, follow the repository’s existing naming, setup, assertion style, fixture strategy, mocking strategy, and test runner conventions.

## Coverage Dimensions

Score each target across these dimensions. Mark an item as covered only when an existing test explicitly verifies it.

| Dimension | Weight | Completion criteria |
|---|---:|---|
| Entry points and success outputs | 15 | All public entry paths and expected success outputs/statuses/results are tested. |
| Input validation and boundaries | 20 | Required fields, type errors, invalid values, edge values, null/empty inputs, and boundary values are covered. |
| Branching and business rules | 15 | Conditional paths, state-dependent behavior, permission/eligibility rules, and domain decisions are covered. |
| Output mapping and side effects | 15 | Response fields, return models, emitted events, persisted state, logs/metrics where relevant, and external calls are asserted. |
| Error handling | 10 | Expected exceptions, error responses, retries, partial failures, and error payloads/messages are asserted. |
| Integration/E2E behavior | 10 | Real runtime, database, filesystem, network boundary, browser, CLI, or application pipeline behavior is covered where applicable. |
| Data variations and collection behavior | 10 | Empty, single, multiple, duplicate, sorted, filtered, paginated, concurrent, or large-data cases are covered where applicable. |
| Test quality and conventions | 5 | Test names, structure, fixtures, assertions, mocks, and setup match repository conventions. |

Calculate the score with a repeatable sub-item method:

1. For each dimension, list the concrete sub-items that apply to the target.
   - Example for input validation: `id = 0`, `id = -1`, missing required field, invalid enum, malformed date.
   - Example for output mapping: one sub-item per returned field, emitted event property, updated state field, or externally visible side effect.
2. Give each sub-item in the same dimension equal value: `dimension weight / applicable sub-item count`.
3. Award a sub-item only when an existing test explicitly verifies it.
4. Do not count non-applicable sub-items. If a whole dimension is not applicable, award the full dimension weight and note why.
5. Sum all earned sub-item values across dimensions.
6. Round the final total to the nearest whole number using standard midpoint rounding away from zero.

Use whole numbers from 0 to 100. Never claim 100% unless every applicable entry path, validation rule, branch, output/side effect, error path, and required integration/E2E scenario is covered.

## Scorecard Format

When auditing or changing tests, produce this scorecard in the final response. If the repository has a required test summary or coverage report convention, include the scorecard there when tests are edited.

```markdown
## Coverage Scorecard

| Metric | Before | After | Delta |
|---|---:|---:|---:|
| Coverage score | {beforeCoverageScore}% | {afterCoverageScore}% | {coverageScoreDelta}% |
| Unit tests | {beforeUnitTestCount} | {afterUnitTestCount} | {unitTestCountDelta} |
| Integration tests | {beforeIntegrationTestCount} | {afterIntegrationTestCount} | {integrationTestCountDelta} |
| E2E tests/scenarios | {beforeE2eScenarioCount} | {afterE2eScenarioCount} | {e2eScenarioCountDelta} |
| Duplicate tests found | {beforeDuplicateTestCount} | {afterDuplicateTestCount} | {duplicateTestCountDelta} |
| Open coverage gaps | {beforeCoverageGapCount} | {afterCoverageGapCount} | {coverageGapCountDelta} |

### Coverage gaps
| # | Area | Gap | Existing coverage | Suggested test |
|---|---|---|---|---|
| 1 | {areaName} | {coverageGapDescription} | {existingTestOrNone} | `{proposedTestName}` |

### Duplicate-test check
| Candidate | Existing matching test/scenario | Decision |
|---|---|---|
| `{candidateTestName}` | `{existingTestOrScenarioName}` | Reuse/extend existing test; do not duplicate. |

### Unit vs integration/E2E placement
| Scenario | Keep as unit test | Keep as integration test | Keep as E2E test | Reason | Action |
|---|---|---|---|---|---|
| {scenarioName} | Yes/No | Yes/No | Yes/No | {placementReason} | Keep/Add/Move/Merge/Remove |
```

## Duplicate-Test Prevention

Before adding a test:

1. Search existing tests for the target class/function/component/route, input values, expected output, error message, branch name, fixture, and scenario title.
2. Search integration/E2E specs, snapshots, golden files, and test summaries for equivalent coverage.
3. Search coverage reports for already-covered files/lines/branches when reports are available.
4. Treat tests as duplicates when they assert the same input, same behavior, same layer, same expected output/error, and same important side effects.
5. Prefer extending an existing test when only missing assertions are output fields, call verification, branch assertions, or state verification.
6. Add a new test only when it covers a distinct branch, input class, boundary, error path, side effect, integration behavior, UI behavior, or workflow behavior.

## Unit vs Integration/E2E Placement Rules

Use these rules to decide where each scenario belongs. The goal is not to remove all overlap; it is to remove duplicated assertions while preserving fast unit confidence and real runtime confidence.

### Keep as Unit Test

Keep a scenario as a unit test when it verifies behavior that can be proven using deterministic inputs, mocks/stubs/fakes, and in-process execution:

- Pure logic, calculations, validators, mappers, formatters, reducers, parsers, and branching rules.
- Input validation short-circuits, including invalid IDs, malformed values, missing required fields, null/empty values, duplicates, and boundary values.
- Exact error messages or structured error objects produced by the unit under test.
- Calls to dependencies using mocks/spies, including call counts and argument values.
- Empty, single-item, multi-item, duplicate, sorted, filtered, and boundary data behavior that does not require real infrastructure.
- Retry, fallback, timeout, and failure handling when dependencies can be reliably simulated.

### Keep as Integration Test

Keep a scenario as an integration test when it verifies collaboration between real modules or adapters but does not require the full user-facing workflow:

- Real dependency injection/container wiring.
- Real database/repository behavior with a test database or isolated storage.
- Serialization/deserialization through real framework bindings.
- Framework middleware, filters, routing/model binding, transactions, migrations, message handlers, or adapter boundaries.
- Contract behavior between application layers.
- External clients replaced by local fakes, testcontainers, in-memory servers, or controlled test doubles.

### Keep as E2E Test

Keep a scenario as an E2E test when it verifies behavior only the full application/runtime can prove:

- Browser/UI flows, CLI flows, mobile flows, or full HTTP/API workflows.
- Authentication/authorization and real user-permission behavior.
- Real persisted state and re-query verification across the application boundary.
- Cross-service workflows, job orchestration, queues/events, or multi-step business processes.
- Critical smoke paths and a small number of representative negative paths.

### Keep in Multiple Layers, But With Different Assertions

Keep a scenario in multiple layers only when each layer asserts different value:

| Scenario type | Unit test should assert | Integration test should assert | E2E test should assert |
|---|---|---|---|
| Happy path read/query | Mapping, branch logic, dependency arguments | Real data access/binding/serialization | User/API receives correct key business result |
| Empty result | Empty data maps to empty output | Query/storage returns empty result correctly | Full workflow handles no-data state gracefully |
| Invalid input | Exact validation error and no dependency call | Framework binding/validation integration | User/API receives expected error status/message |
| Write/command success | Command construction and dependency calls | Transaction/persistence effect in test store | User-visible state changed after full workflow |
| Permission denied | Permission branch and no unsafe operation | Authorization policy wiring | Real user with insufficient rights is blocked |
| External failure | Retry/fallback/error mapping | Adapter/client error handling | User-visible failure behavior or alerting |

### Remove, Merge, or Reclassify

Recommend removing, merging, or reclassifying a test when:

- Tests in the same layer assert the same behavior with only cosmetic input differences.
- A unit, integration, and E2E test all assert only the same status/output without layer-specific value.
- Multiple E2E scenarios differ only by scalar validation values that unit tests can cover faster.
- A broad E2E test repeats every mapper/output-field assertion already covered by unit tests; keep only key business assertions in E2E.
- A unit test depends on real infrastructure, global state, wall-clock time, network, filesystem, browser, or database; move it to integration/E2E or isolate it.
- An E2E scenario verifies only pure validation without pipeline-specific value; keep it as unit and retain at most one representative integration/E2E validation scenario.

## Recommendation Procedure

For every suggested or existing scenario:

1. Classify it as `Unit only`, `Integration only`, `E2E only`, `Multiple layers with distinct assertions`, or `Duplicate/merge`.
2. State the reason using the placement rules above.
3. If it belongs in unit tests, propose the exact test name following repository conventions.
4. If it belongs in integration tests, propose the exact test name or spec/scenario title.
5. If it belongs in E2E tests, propose the exact scenario title and tag/marker if the repository uses them.
6. If it belongs in multiple layers, list the distinct assertions for each layer so developers do not duplicate the same checks.
7. If it is duplicate, identify the existing test/scenario to keep and the one to remove, merge, or extend.

## Test Naming and Structure Rules

Follow the repository’s existing conventions. If no convention is visible, use these defaults:

- Unit test method/function: `{methodOrSubject}_{expectedOutcome}_when_{condition}` or language-idiomatic equivalent.
- Integration test: `{workflowOrAdapter}_{expectedOutcome}_when_{condition}`.
- E2E scenario: plain-language behavior statement, optionally tagged with the repository’s positive/negative/smoke/regression markers.
- Keep test names behavior-focused, not implementation-focused.
- Group tests by scenario category when the framework supports it: happy path, validation/error cases, boundaries/corner cases, integration behavior, and regression cases.
- Prefer table-driven/parameterized tests for equivalent scalar validation values.

## Test Quality Checklist

Every added or modified test should follow this checklist:

- Uses the repository’s chosen test framework and assertion style.
- Reuses existing setup helpers, fixtures, factories, builders, test data utilities, and cleanup patterns.
- Avoids hardcoded secrets, credentials, production URLs, real personal data, and environment-specific assumptions.
- Uses deterministic data and controls time/randomness where possible.
- Verifies all important outputs, errors, side effects, dependency calls, or persisted state for the layer being tested.
- Avoids duplicated assertions across layers unless each layer proves different value.
- Keeps tests independent, readable, and isolated from order dependence.
- Cleans up external state or uses isolated test resources.
- Documents any known untestable/manual-verification area.

## Gap-to-Test Suggestion Procedure

For each uncovered gap:

1. State the target branch, rule, output, side effect, or workflow that is missing coverage.
2. Check for duplicates and decide whether to extend an existing test or add a new test.
3. Propose the exact unit, integration, or E2E test name/scenario title.
4. Identify required setup data, mocks/fakes, fixtures, environment, or external resources.
5. Identify assertions:
   - Status/result/return value.
   - Response body, output object, rendered UI, CLI output, file output, event, or error payload.
   - State changes, database changes, emitted events, logs/metrics where relevant.
   - Dependency call count and arguments where relevant.
6. Explain how the test increases the score and why it belongs in that layer.

## Targeting 100% Coverage

To target 100%, suggestions must include tests for:

- Every public entry path and important private branch reachable through public behavior.
- Every validation rule and boundary condition.
- Every mapper/output branch and externally visible field or side effect.
- Empty, single-item, multi-item, duplicate, sorted, filtered, paginated, and error data where applicable.
- Not-found, unauthorized/forbidden, timeout, retry, cancellation, conflict, and failure paths when supported.
- State changes and persistence for command/write workflows.
- At least one representative integration/E2E path when runtime wiring, framework binding, real persistence, browser behavior, or authorization matters.

If 100% is not realistic because external systems, missing instrumentation, brittle legacy behavior, unmockable dependencies, or repository constraints block coverage, state the blocker and provide the highest achievable score with remaining manual-verification items.

## Editing Rules

When the user asks to implement coverage improvements:

1. Do not write duplicate tests.
2. Prefer adding assertions to existing tests before creating new tests.
3. Follow the existing test style, naming, fixtures, and helper patterns.
4. Update any repository-required test summary, snapshot, golden file, or coverage note after test changes.
5. Run the relevant test command discovered from the repository after code/test changes when tooling is available.
6. Include before/after score and duplicate-test decision in the final response.

## Completion Criteria

The task is complete when:

- The target production code and matching tests have been reviewed.
- Duplicate-test analysis is documented.
- Coverage score before and after is reported.
- Unit vs integration/E2E placement is clear for every suggested or duplicate scenario.
- All actionable gaps have either a test added, an existing test extended, or a clear suggestion.
- Required test summaries/reports are updated when tests change.
- Relevant tests have passed or blockers are clearly reported.
