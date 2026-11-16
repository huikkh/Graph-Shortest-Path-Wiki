---
source_id: R2
publisher: NetworkX Developers
source_title: Shortest Paths
source_version: NetworkX 3.7 documentation
url: https://networkx.org/documentation/stable/reference/algorithms/shortest_paths.html
source_type: official_software_documentation
review_status: pending_user_approval
ingest_status: not_ingested
proposed_used_by:
  - Shortest Path Problem
  - Algorithm Comparison
---

# R2 — NetworkX: Shortest Paths（來源審查紀錄）

## 1. 來源摘要

NetworkX 官方文件說明最短路徑問題的定義、加權與非加權圖的差異、有向與無向圖的路徑限制，以及不同最短路徑演算法的選擇。文件也區分 single-pair、single-source、all-pairs 等查詢類型，並提供 NetworkX API 的使用說明。

本次審查以文件中的「Shortest Paths」、「Algorithms」與「Simplified Interface」段落為主；不將大量 API 函式清單或進階查詢技巧全部匯入教學 Wiki。

## 2. 建議擷取的知識

1. **最短路徑問題的定義**：在圖中尋找總距離／成本最小的路徑。
2. **非加權圖的最短路徑**：以經過的邊數最少為目標。
3. **加權圖的最短路徑**：以路徑上邊權重總和最小為目標。
4. **方向限制**：無向圖的邊可雙向行走；有向圖必須遵守邊的方向。
5. **查詢範圍**：single-pair（指定起點與終點）、single-source（指定起點到所有可達節點）、all-pairs（所有節點對）。
6. **演算法初步選擇**：BFS 適用於非加權圖；Dijkstra 適用於非負權重；Bellman-Ford 可處理負權重邊。
7. **複雜度比較的參考值**：文件提供 BFS、Dijkstra、Bellman-Ford 的典型時間複雜度，可作為比較頁的初步依據；詳細前提仍須由演算法專頁與資料結構專頁補足。

## 3. 與 R1 的重複內容及處理方式

R1（Cornell CS 2110: 21. Graphs）已介紹 Graph、Weighted Graph、Directed／Undirected Graph、Path，以及加權路徑成本。因此 R2 不應再次完整定義這些基礎名詞。

R2 應聚焦於「如何定義並分類最短路徑問題」：

- 引用 R1 的 Graph & Weighted Graph 頁面作為前置知識。
- 只簡短回顧路徑成本，接著說明如何最小化該成本。
- 把有向／無向圖的行走限制濃縮成最短路徑的注意事項，避免重複 R1 的完整說明。
- 新增 R1 尚未涵蓋的查詢範圍分類與演算法選擇概觀。

## 4. 需要避免的誤解

- 不可將「最短」一律解釋成邊數最少；這只適用於非加權圖（或所有邊成本相同的情況）。加權圖應比較總權重。
- 不可把 Dijkstra 描述成適用於所有加權圖；它要求邊權重非負。
- 不可把 Bellman-Ford 的負權重能力誤解為能在存在可達負權重環時得到有限的最短距離。負環問題留待 Bellman-Ford 專頁詳細說明。
- NetworkX 文件的複雜度表是概觀；Time Complexity 頁面仍需交代圖表示法、優先佇列實作及分析前提。
- NetworkX 的 `weight` 參數與缺少指定 edge attribute 時預設權重為 1，屬於特定 API 行為，不應寫成最短路徑問題的一般數學定義。若 Wiki 不以 NetworkX 程式實作為主，建議不放入主體，最多作為補充註記。

## 5. 建議對應的 Wiki 頁面

### 主要頁面

- `03-shortest-path-problem.md`

### 次要頁面

- `10-algorithm-comparison.md`

R2 可以為 Dijkstra、BFS、Bellman-Ford 提供概觀比較，但各演算法的細節、限制與複雜度推導應以對應專頁及其他專門來源補足。

## 6. 建議的 Shortest Path Problem 頁面大綱

1. 本頁目的與最短路徑問題定義
2. 非加權圖：最少邊數
3. 加權圖：最小總權重
4. 有向圖與無向圖的方向限制
5. 三種查詢範圍：single-pair、single-source、all-pairs
6. 如何初步選擇演算法：BFS、Dijkstra、Bellman-Ford
7. 一個簡單例子：同一張圖上，最少邊數路徑不一定是最低總成本路徑
8. 與 Graph & Weighted Graph、Dijkstra、BFS、Bellman-Ford、Algorithm Comparison 的連結
9. 來源與延伸閱讀

## 7. Review 結論

**建議：通過 Review，待使用者確認後再 Ingest。**

理由：來源為 NetworkX 官方文件，對最短路徑定義、問題分類與演算法選擇提供直接且高度相關的說明。透過限定擷取範圍，可避免與 R1 重複，並補上查詢類型與演算法選擇概觀。

## 8. 原始來源

- NetworkX Developers, *Shortest Paths*, NetworkX 3.7 documentation.
- https://networkx.org/documentation/stable/reference/algorithms/shortest_paths.html
- 本次審查重點：Shortest Paths、Algorithms、Simplified Interface。
