# Ingest Log

## R1 — Cornell CS 2110: 21. Graphs

- Review：Approved
- Local Markdown page created: `wiki/02-graph-weighted-graph.md`
- Source record created: `sources/R1.md`
- Source metadata retained: Yes
- Internal links added: Shortest Path Problem, Dijkstra Algorithm, BFS, Bellman-Ford (targets planned; some not yet created)
- Content check: Definitions, path-cost explanation, representation trade-offs, and non-negative-weight caveat reviewed against R1.

## R2 — NetworkX: Shortest Paths

- Review：Approved by user.
- Local Markdown page created: `wiki/03-shortest-path-problem.md`
- Source record created: `sources/R2.md`
- Main knowledge added: shortest-path definition; unweighted vs weighted path cost; directed vs undirected traversal constraints; single-pair, single-source, all-pairs query types; introductory algorithm-selection guide.
- Duplicate-content handling: Graph, Weighted Graph, Path, and base path-cost definitions remain in R1 page; R2 links to R1 and only recalls what is needed.

## R3 — Cornell CS 2110: 23. Shortest Paths

- Review：Approved by user.
- Local Markdown page created: `wiki/04-dijkstra-algorithm.md`
- Source record created: `sources/R3.md`
- Main knowledge added: Dijkstra's non-negative-weight precondition; undiscovered/frontier/settled states; `dist` and `prev`; greedy selection; Dijkstra invariant; priority-queue updates; early stopping after settling a target; path reconstruction; implementation-specific complexity assumptions.
- Existing-content integration: retained the user's original A–F undirected weighted-graph example and verified its settlement order and final distances.
- Pseudocode: standardized on an indexed min-priority queue with `decrease_key`; lazy deletion is described separately so the two implementation models are not mixed.

## R4 — Cornell CS 2110: 18. Heaps and Priority Queues

- Review：Approved by user on 2026-11-16.
- Local Markdown page created: `wiki/06-priority-queue.md`
- Source record created: `sources/R4.md`; review record updated: `sources/R4-review.md`.
- Main knowledge added: Priority Queue ADT; Min-Priority Queue vs Max-Priority Queue; Binary Heap shape/order invariants; array representation; Min-Heap insert / Bubble Up, peekMin, extractMin / Bubble Down; single-operation complexity; Dijkstra priority update context.
- Source nuance: Cornell lecture primarily demonstrates Max Heap and frames Min Heap as an exercise. Wiki carefully applies the symmetric Min-Heap order rule; it does not attribute a complete indexed `decrease_key` implementation to R4. Dijkstra usage references R3.
- Cross-links: Dijkstra Algorithm, Edge Relaxation, Time Complexity, Shortest Path Problem.
- Local checks: page frontmatter present; source IDs and expected paths verified; no duplicate filename/title; formulas and complexity statements reviewed.
- External LLM Wiki Lint: Not run; no external LLM Wiki engine is connected.

## R5 — MIT 6.006: Bellman-Ford

- Review：Approved by user on 2026-11-16.
- Local Markdown page created: `wiki/09-bellman-ford.md`.
- Source record created: `sources/R5.md`; review record updated: `sources/R5-review.md`.
- Main knowledge added: negative-weight edges vs negative-weight cycles; source reachability; `dist` / `prev` initialization; repeated relaxation of all edges; why at most `|V|-1` rounds suffice when no relevant negative cycle exists; an additional edge scan for reachable negative-cycle detection; early stopping; path reconstruction; `O(VE)` time and `O(V)` auxiliary space.
- Teaching example: a directed acyclic graph with one negative edge, demonstrating that negative edges do not necessarily imply a negative cycle.
- Correctness safeguards: skip edges whose tail has `dist[u] = infinity`; explain that only vertices reachable from a source-reachable negative cycle are affected with unboundedly decreasing path costs; distinguish cycle detection from extracting a cycle witness.
- Duplicate handling: Graph / Weighted Graph reuse R1; SSSP framing reuses R2; Dijkstra restriction and comparison reuse R3; generic relaxation is reserved for page 05; full complexity comparison is reserved for page 07.
- Cross-links: Shortest Path Problem, Dijkstra Algorithm, Edge Relaxation, Time Complexity, BFS, Algorithm Comparison.
- Local checks: page frontmatter, references, internal-link targets, numeric example, pseudocode guards, and source metadata reviewed.
- External LLM Wiki Lint: Not run; no external LLM Wiki engine is connected.

## R6 — Cornell CS 312: Lecture 26, Finding Shortest Paths

- Review：Approved by user on 2026-11-16.
- Ingest：Completed in the local Markdown Wiki on 2026-11-16.
- Updated page：`wiki/04-dijkstra-algorithm.md` (supplemented existing page; no duplicate Dijkstra page created).
- Source record created: `sources/R6.md`; review record updated: `sources/R6-review.md`.
- Main knowledge added: Shortest-Path Tree; how selected `prev` edges form a rooted tree over source-reachable vertices; predecessor / optimal-substructure property; uniqueness caveat when multiple shortest paths exist.
- Example integration: reused the existing A–F Dijkstra example and its predecessor values; no distances or pseudocode were rewritten.
- Duplicate handling: Greedy Strategy, Frontier／Settled, nonnegative-weight condition, priority queue, early stopping, and complexity remain supported by R3／R4.
- Page references updated: R6 added to Dijkstra frontmatter and reference list.
- External LLM Wiki Lint：Not run; no external LLM Wiki engine is connected.


## Current project status

- Approved/Ingested external sources: 6 (R1, R2, R3, R4, R5, R6).
- Completed knowledge pages: 10 / 10 (01–10).
- R6 was integrated into the existing Dijkstra page.
- Page 05 Edge Relaxation synthesizes approved sources R3 and R5.
- Pages 01 Overview, 07 Time Complexity, 08 BFS, and 10 Algorithm Comparison were created from already approved sources at the user's request.
- Next stage: use the completed Wiki as the knowledge base to plan and generate the teaching HTML slide deck.

## Edge Relaxation — integrated concept page from approved sources R3 and R5

- User instruction：Approved to start creating page 05 on 2026-11-16.
- Local Markdown page created: `wiki/05-edge-relaxation.md`.
- No new external source was ingested; the page synthesizes already approved sources R3 (Cornell CS 2110, 23. Shortest Paths) and R5 (MIT 6.006, Lecture 12 / Recitation 12: Bellman-Ford).
- Main knowledge added: candidate-distance calculation; strict-improvement condition; updates to `dist` and `prev`; finite-distance guard; directed / undirected edge handling; why equal candidates do not require predecessor replacement.
- Algorithm integration: contrasts Dijkstra's relaxation of outgoing edges from settled vertices with Bellman-Ford's repeated scans of all edges for up to `|V|-1` rounds and a final reachable-negative-cycle check.
- Teaching example: `A → B (7)`, `A → C (2)`, `C → B (3)`; relaxation improves `dist[B]` from 7 to 5 and updates `prev[B]` from A to C.
- Cross-links: Graph & Weighted Graph, Shortest Path Problem, Dijkstra Algorithm, Priority Queue, Bellman-Ford, Time Complexity.
- Duplicate handling: no new source ID or duplicate algorithm page created; shared operation is separated from each algorithm's scheduling strategy.
- Local checks: frontmatter, references, internal link targets, formula consistency, numeric example, pseudocode guard, and source mapping checked.
- External LLM Wiki Lint: Not run; no external LLM Wiki engine is connected.


## Remaining knowledge pages — completed locally on 2026-11-16

### 07 — Time Complexity

- User request：完成剩餘 Wiki 頁面。
- Local Markdown page created: `wiki/07-time-complexity.md`.
- No new external source ingested; synthesizes approved sources R1–R5.
- Main knowledge added: Big-O intuition; V and E; adjacency-list vs adjacency-matrix storage; BFS complexity; Dijkstra complexity under indexed binary heap and linear-scan/matrix assumptions; Bellman-Ford complexity; auxiliary vs total space; sparse vs dense graph intuition; implementation caveats.
- Complexity safeguards: Dijkstra binary-heap bound stated as `O((V+E) log V)`; `O(E log V)` identified as a common simplification under suitable graph assumptions. Lazy deletion is not conflated with indexed decrease-key. Graph storage and auxiliary storage are distinguished.

### 08 — BFS

- Local Markdown page created: `wiki/08-bfs.md`.
- No new external source ingested; synthesizes approved sources R1–R3, especially R2 NetworkX Shortest Paths and R3 Cornell Shortest Paths.
- Main knowledge added: BFS layer-by-layer exploration; FIFO Queue; visited, dist, prev; unweighted shortest-path guarantee; directed/undirected handling; pseudocode; path reconstruction; adjacency-list and adjacency-matrix complexity; limitations on differently weighted graphs.
- Teaching example: six-vertex graph from A, illustrating levels and predecessor-based path reconstruction.

### 10 — Algorithm Comparison

- Local Markdown page created: `wiki/10-algorithm-comparison.md`.
- No new external source ingested; synthesizes approved sources R2–R5.
- Main knowledge added: BFS vs Dijkstra vs Bellman-Ford comparison; edge-weight preconditions; core strategies; data structures; negative-weight support; reachable negative-cycle detection; typical single-source time bounds; algorithm-selection guide; common misconceptions.
- Comparison safeguards: clarifies that BFS minimizes edge count, Dijkstra requires nonnegative weights, and Bellman-Ford handles negative edges while reporting source-reachable negative cycles. Vertices affected by a reachable negative cycle do not have finite shortest distances; not every vertex in the graph is necessarily affected.

### 01 — Overview

- Local Markdown page created: `wiki/01-overview.md`.
- No new external source ingested; summarizes the six already approved sources R1–R6 and links all nine concept/algorithm pages.
- Main knowledge added: Wiki scope, learning outcomes, beginner reading path, knowledge map, algorithm overview, glossary, source governance and workflow, and next-step guidance for the HTML teaching slides.

### Final local checks

- All 10 planned Wiki pages now exist.
- Internal wiki links resolve to existing page files.
- Each page has YAML-style metadata including title, summary, page_type, related_concepts and references.
- All source IDs referenced by pages are present in `sources/`.
- No duplicate page titles or filenames detected.
- Local checks performed on Markdown/frontmatter, links, source IDs, and selected numerical examples.
- External LLM Wiki engine Lint: Not run; no external LLM Wiki engine is connected.

## HTML 教學簡報 — 以已完成 Wiki 作為知識庫

- 日期：2026-11-16。
- 簡報：`presentation/index.html`，共 14 張投影片。
- 知識來源：已建立的 10 個 Wiki 頁面；無新增外部來源。
- 來源對照：`presentation/content-map.md`。
- Workflow 紀錄：`workflow/html-slide-workflow.md`。
- 互動：上一頁／下一頁、方向鍵、PageUp／PageDown、Home／End、全螢幕、Dijkstra 六輪步驟控制、小測驗。
- Dijkstra 範例沿用 Wiki 04，圖例距離與路徑成本已核對。
