---
source_id: R3
institution: Cornell University
course: CS 2110
source_title: 23. Shortest Paths
term: 2026 Fall
source_type: university_course_lecture_notes
url: https://courses.cs.cornell.edu/courses/cs2110/2026fa/lectures/lec23/
used_by:
  - Dijkstra Algorithm
  - Edge Relaxation
  - Priority Queue
  - Time Complexity
review_status: approved
ingest_status: completed-in-local-wiki
---

# R3 Review — Cornell CS 2110: 23. Shortest Paths

## 1. 來源摘要

Cornell CS 2110 Fall 2026 第 23 講從 BFS 的無權重最短路徑延伸到加權圖，逐步建立 Dijkstra。教材說明 Dijkstra 的非負權重前提、frontier / settled 狀態、暫定距離、前驅頂點、最小優先佇列、距離更新、Dijkstra invariant、執行追蹤及時間／空間複雜度。

原始來源：<https://courses.cs.cornell.edu/courses/cs2110/2026fa/lectures/lec23/>

## 2. 建議擷取範圍

### 納入 Dijkstra Algorithm 頁面（04）

- 問題設定：給定來源頂點，在所有邊權重非負的加權圖中，求來源點到各可達頂點的最短距離。
- 三種頂點狀態：undiscovered、frontier（discovered but not settled）、settled。
- `dist[v]`：目前已知的來源點到頂點 v 的最短路徑距離估計。
- `prev[v]`：目前最佳路徑上 v 的前一個頂點，用來還原路徑。
- Greedy Strategy：每輪從 frontier 中選出暫定距離最小的頂點，並將它 settled。
- Dijkstra invariant：被選為最小暫定距離的 frontier 頂點，其距離已是最終最短距離。
- 演算法初始化、主迴圈、終止條件及路徑重建概念。
- 一個逐輪執行範例；可沿用本 Wiki 原有 A–F 範例，避免另建重複範例。
- 適用限制：所有邊權重必須非負；零權重允許，負權重不允許。

### 作為其他頁面的補充連結

- **Edge Relaxation（05）**：說明候選距離 `dist[u] + w(u,v)`，若小於目前 `dist[v]`，就更新 `dist[v]` 與 `prev[v]`。R3 可提供 Dijkstra 中的使用情境；完整概念及跨演算法比較留在 05。
- **Priority Queue（06）**：說明為何 FIFO Queue 不足以處理加權圖，以及 Min-Priority Queue 如何依暫定距離取出下一個頂點。Binary Heap 的內部細節留在 06。
- **Time Complexity（07）**：保留 R3 的實作假設、操作成本與空間分析，完整比較不同表示法與佇列實作留在 07。

## 3. 與現有 Wiki 的重複內容比對

| 概念 | R1 Graph | R2 Shortest Path | R3 建議處理 |
|---|---|---|---|
| Graph、Vertex、Edge | 已完整介紹 | 有回顧 | 不重複定義，連回 02 |
| Weighted Graph、Path Cost | 已介紹 | 已解釋路徑成本 | 只說明 Dijkstra 所需的非負權重條件 |
| Single-source shortest paths | — | 已介紹 | 在 Dijkstra 頁面說明它是本演算法的問題範圍，不重寫一般定義 |
| Dijkstra 的選擇規則 | — | 只有概觀 | 詳細納入 04 |
| Greedy Strategy | — | 僅提及 Dijkstra 核心想法 | 在 04 簡單解釋，不新增獨立頁面 |
| Edge Relaxation | — | 只提到演算法會更新距離 | 04 提供應用摘要，詳細內容留 05 |
| Priority Queue | — | 只有演算法概觀 | 04 說明用途，資料結構原理留 06 |
| Time Complexity | — | 只提供概觀 | 04 保留摘要與前提，完整分析留 07 |
| BFS、Bellman-Ford | — | 已比較 | 僅連結 08、09、10，不重複其完整內容 |

## 4. 需要注意的技術細節

1. **非負權重是必要條件**：教材說明，非負權重確保路徑後段的額外邊不會降低總成本，這是最小 frontier 頂點能安全 settled 的關鍵。零權重邊可以接受；負權重邊不適用 Dijkstra。
2. **不要把暫定距離說成已確定距離**：只有當頂點由 Min-Priority Queue 取出並依 Dijkstra invariant settled 後，其距離才可視為最終最短距離。
3. **距離改善時要更新前驅與佇列優先順序**：若 `dist[u] + w(u,v) < dist[v]`，需同時更新 `dist[v]`、`prev[v]` 與 frontier 中 v 的 priority。
4. **Queue 與 Priority Queue 不可混用**：一般 FIFO Queue 不保證依目前暫定距離取出頂點；加權圖版本需要 Min-Priority Queue。
5. **路徑還原**：距離表只能回答成本；若要還原實際路徑，需記錄 `prev` 並從目的頂點反向追蹤至來源點。
6. **有向／無向圖的說法**：R3 以有向非負加權圖說明。Dijkstra 也可用於非負權重無向圖；可將每條無向邊視為兩條反向有向邊。若採用此一般化說法，建議另以補充來源或明確說明轉換方式支持，不要誤以為 R3 的課堂設定涵蓋所有圖型。
7. **複雜度不可脫離實作前提**：R3 的程式模型使用 adjacency-list 風格的 outgoing edges、heap-backed dynamic priority queue，以及預期 O(1) 的 HashMap 操作。教材分析得到 expected O(E log V)（其分析背景包含連通圖／可達頂點與邊數的關係）；本 Wiki 的一般摘要宜保留更完整的 O((V+E) log V)，並在 07 說明連通性、圖表示法及 decrease-key 實作假設。
8. **Priority Queue 的更新命名**：以 R3 的 `updatePriority` 概念為準；當距離變小時，Min-Priority Queue 中該元素的 key 也變小。避免將「距離減少」誤寫成數值優先權增加。

## 5. 與既有 Dijkstra 草稿的整合建議

對話中已提供一份 Dijkstra Algorithm 草稿，內容已有 `dist`、`prev`、`settled`、Min-Priority Queue、A–F 執行範例、虛擬碼、非負權重原因及複雜度。正式 Ingest 時應以該草稿為既有內容基礎，利用 R3 補強以下部分：

- 明確定義 undiscovered、frontier、settled 三種狀態。
- 補上 Dijkstra invariant 的初學者版解釋。
- 說明為何 FIFO Queue 不夠，以及為何必須依距離取出 frontier 頂點。
- 修整虛擬碼，使 `settled` 的宣告與實際使用一致；若使用 decrease-key 並確保每個頂點僅有一筆佇列項目，則不需要 lazy-deletion 的 stale-entry 判斷。若採用重複 push 的 lazy-deletion 寫法，則必須在取出時跳過過期項目。
- 不要覆蓋既有 A–F 範例；先驗算數值，再保留一個完整範例即可。

## 6. 建議的 Ingest 範圍與目標頁面

- 主要頁面：`wiki/04-dijkstra-algorithm.md`
- 次要連結：`wiki/03-shortest-path-problem.md`、`wiki/05-edge-relaxation.md`、`wiki/06-priority-queue.md`、`wiki/07-time-complexity.md`、`wiki/08-bfs.md`、`wiki/09-bellman-ford.md`、`wiki/10-algorithm-comparison.md`
- R3 中 BFS 的詳細原理不重複匯入 BFS 頁面；Bellman-Ford 也只保留 Dijkstra 不適用於負權重時的簡短交叉引用。

## 7. Review 結論

**建議通過 Review，等待使用者核准後 Ingest。**

理由：R3 是 Cornell CS 2110 官方課程教材，針對 Dijkstra 的核心原理、正確性直覺、資料結構需求及實作追蹤有完整教學。其內容能補足 R2 只有演算法選擇概觀的不足，而且可透過頁面分工避免重複匯入。

### 審查檢查表

- [x] 來源與 Wiki 主題高度相關。
- [x] 已辨識與 R1、R2 的重複內容。
- [x] 已將 Dijkstra 核心內容對應到頁面 04。
- [x] 已將鬆弛、優先佇列、複雜度細節分配到專屬頁面。
- [x] 已標示非負權重、Queue／Priority Queue、前驅紀錄等重要細節。
- [x] 已標示複雜度與圖型假設需避免過度泛化。
- [x] 使用者核准。
- [x] 正式 Ingest：已建立 `wiki/04-dijkstra-algorithm.md`。
- [x] 本地 Markdown／中繼資料／內部連結檢查。
- [ ] 外部 LLM Wiki 引擎 Lint（未連接外部引擎）。
