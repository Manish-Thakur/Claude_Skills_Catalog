---
name: data-structures-algorithms
description: 'Use when: analyzing code for essential data structures, common algorithms, collection choices, query/pipeline patterns, traversal/search/sort logic, caching, batching, and time or space complexity. Helps explain performance tradeoffs across languages, frameworks, services, UI code, integrations, reporting, and data-processing workflows.'
argument-hint: 'Scope, files, method, class, or feature area to analyze'
---

# Data Structures, Algorithms, and Complexity Analysis

## Outcome

Produce a practical explanation of the essential data structures, common algorithms, and time/space complexity used in the requested code area. Tie findings to real code paths, files, symbols, and observed behavior whenever possible.

## When to Use

Use this skill for requests such as:
- "Explain the data structures used in this feature"
- "Analyze algorithm complexity in this code path"
- "Find expensive loops, queries, sorting, grouping, or lookup patterns"
- "Compare collection choices like lists, arrays, maps/dictionaries, sets, queues, stacks, tables, streams, or observable collections"
- "Teach me essential DS&A concepts from this codebase"
- "Review performance tradeoffs in services, batch jobs, imports, reports, UI code, or data-processing workflows"

## Workflow

1. **Define the scope**
   - Identify the feature area, project, files, class, method, endpoint, job, component, or workflow under review.
   - If the request is broad, inspect the project structure and start with likely entry points such as controllers, services, handlers, jobs, view models, components, repositories, utilities, or tests.

2. **Map the relevant code path**
   - Locate entry points, public methods, event handlers, commands, subscriptions, workers, service calls, and helper classes/functions.
   - Trace the primary data flow: inputs, transformations, storage, lookup, sorting/grouping, output, and side effects.
   - Prefer actual symbol/file references over generic explanations.

3. **Inventory data structures**
   - List collections and structures used, such as lists, arrays, maps/dictionaries, sets, queues, stacks, trees, graphs, heaps, tables, streams/iterables, observable collections, custom registries, caches, DTOs, and domain models.
   - For each, explain:
     - What it stores
     - Why it appears to be used
     - Key operations: add, remove, lookup, iteration, grouping, sorting, binding, serialization, or persistence
     - Expected complexity and memory behavior

4. **Identify algorithms and patterns**
   - Look for common patterns:
     - Linear scan/filter: usually $O(n)$
     - Nested loops or pairwise comparison: often $O(nm)$ or $O(n^2)$
     - Hash/map lookup: average $O(1)$ per lookup
     - Sorting: typically $O(n \log n)$
     - Grouping/indexing: often $O(n)$ additional memory
     - Tree/graph traversal, dependency traversal, merge/reconciliation, batching, pagination, caching, memoization, deduplication, and streaming
   - Include language-specific query/pipeline operations, such as LINQ, streams, comprehensions, SQL-like queries, map/filter/reduce, grouping, ordering, joins, materialization, and repeated enumeration.

5. **Estimate complexity**
   - Define variables clearly, for example: $n$ = items, $m$ = related items, $r$ = rows, $k$ = groups, $d$ = dependency depth, $e$ = edges.
   - Provide time and space complexity for the main path and any important helper paths.
   - Separate database, network, filesystem, and external service cost from in-memory complexity where relevant.
   - Call out lazy/deferred execution, repeated enumeration, materialization, serialization, rendering, and UI binding costs.

6. **Assess performance risks**
   - Flag likely hotspots:
     - Nested loops over large datasets
     - Query or filtering work inside loops
     - Repeated materialization, sorting, grouping, joins, or external calls
     - Linear lookup where a map/dictionary or set would help
     - Large table/object graph materialization
     - UI-thread work over large collections
     - Inefficient string building, reflection, serialization, or repeated conversions
    - Label a risk as confirmed if the code path is unconditionally executed at runtime with unbounded or large input; label it theoretical if it depends on unknown data volumes, optional branches, or external factors.

7. **Recommend improvements**
   - Suggest small, idiomatic improvements first:
     - Precompute maps/dictionaries or sets for repeated lookup
     - Move query/materialization outside loops
     - Stream, paginate, or batch large inputs
     - Avoid unnecessary sorting or repeated grouping
     - Use efficient string-building or buffering APIs
     - Batch UI-bound collection updates where possible
     - Add indexes or reshape queries only when database details are visible
   - Include tradeoffs: readability, memory, ordering, duplicate handling, concurrency, null/error handling, and behavior changes.

8. **Teach the concepts through code examples**
   - Explain DS&A concepts in terms of the actual code being reviewed.
   - Keep examples concise and avoid rewriting large sections unless asked.
   - If recommending code changes, preserve existing project style, language idioms, and framework constraints.

## Reporting Format

Use this structure unless the user asks otherwise:

1. **Scope analyzed**: files, symbols, and assumptions.
2. **Data structures**: table with structure, purpose, operations, complexity, and notes.
3. **Algorithms/patterns**: concise explanation with complexity.
4. **Complexity summary**: main path using defined variables.
5. **Risks and improvement ideas**: prioritized and behavior-safe.
6. **Learning takeaway**: the key DS&A concept demonstrated by this code.

## Quality Checks

Before finalizing:
- Verify every file and symbol reference exists in the workspace.
- If a referenced file or symbol cannot be located in the workspace, explicitly state it is missing, list what was found that is closest in name or purpose, and ask the user to confirm the correct target before proceeding.
- Define all complexity variables.
- Avoid claiming database, network, filesystem, or external service complexity unless implementation details are visible.
- State uncertainty when runtime sizes, indexes, external service behavior, or production data volumes are unknown.
- Prefer actionable code-specific observations over generic textbook content.
