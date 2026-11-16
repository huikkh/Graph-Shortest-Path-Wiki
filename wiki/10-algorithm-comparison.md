---
title: Algorithm Comparison
aliases:
  - 最短路徑演算法比較
  - BFS、Dijkstra、Bellman-Ford 比較
summary: 本頁比較 BFS、Dijkstra 與 Bellman-Ford 的適用條件、核心策略、負權重處理能力及時間複雜度，協助依照圖的特性選擇演算法。
page_type: comparison
related_concepts:
  - Graph & Weighted Graph
  - Shortest Path Problem
  - BFS
  - Dijkstra Algorithm
  - Bellman-Ford
  - Edge Relaxation
  - Priority Queue
  - Time Complexity
references:
  - R2
  - R3
  - R4
  - R5
status: reviewed-and-ingested
---

# Algorithm Comparison（最短路徑演算法比較）

## 1. 本頁目的

本頁統整 BFS、Dijkstra 與 Bellman-Ford 三種單源最短路徑方法。讀完後，應能依照圖是否加權、是否存在負權重邊、是否需要負環偵測，以及所需的執行效率，選擇適合的演算法。

建議先閱讀 [[03-shortest-path-problem|Shortest Path Problem]]，再依序了解 [[08-bfs|BFS]]、[[04-dijkstra-algorithm|Dijkstra Algorithm]] 與 [[09-bellman-ford|Bellman-Ford]]。

## 2. 三種演算法的核心差異

| 比較項目 | BFS | Dijkstra | Bellman-Ford |
|---|---|---|---|
| 主要用途 | 非加權圖的最少邊數路徑 | 非負權重圖的最低總成本路徑 | 可包含負權重邊的最低總成本路徑 |
| 邊權重條件 | 非加權，或每條邊成本相同且非負 | 所有邊權重皆須非負；允許 0 | 可以有負權重邊；需檢查來源點可達的負權重環 |
| 核心策略 | FIFO Queue，逐層探索 | Greedy；每輪確定暫定距離最小的 Frontier 頂點 | 最多 \(V-1\) 輪反覆掃描所有邊並 Relaxation |
| 常用資料結構 | FIFO Queue | Min-Priority Queue | `dist`、`prev` 陣列與邊集合 |
| 是否使用 Edge Relaxation | 可用首次發現時的距離設定表達 | 是 | 是 |
| 負權重邊 | 不以權重總和為目標；不能保證加權最短路徑 | 不支援 | 支援 |
| 負權重環偵測 | 不提供 | 不提供 | 可偵測從來源點可達的負權重環 |
| 單源時間複雜度（鄰接串列） | \(O(V+E)\) | Binary Heap：\(O((V+E)\log V)\) | \(O(VE)\) |

複雜度是常見實作的漸進上界；Dijkstra 的複雜度尤其依賴優先佇列與圖表示法。詳細前提請見 [[07-time-complexity|Time Complexity]]。[R2][R3][R4][R5]

## 3. 如何選擇演算法？

可以按照以下順序判斷：

1. **每條邊成本是否相同？** 若是，且目標是找最少邊數／最低等成本路徑，使用 BFS。
2. **若邊權重不同，是否保證所有權重非負？** 若是，通常使用 Dijkstra。
3. **若存在負權重邊，是否需要求單源最短路徑？** 使用 Bellman-Ford，並檢查來源點可達的負權重環。
4. **若有來源點可達的負權重環？** 受該負環影響的頂點不存在有限最短距離；演算法應回報或標示此狀況，而不是輸出一般有限距離。

NetworkX 的演算法選擇文件也將 BFS 用於非加權圖、Dijkstra 用於非負權重圖、Bellman-Ford 用於可能含負權重的圖，並列出各自的典型複雜度。[R2]

## 4. 用同一個問題理解差異

假設要從起點 S 找到其他頂點的最短路徑：

- **若每條邊都代表「走一步」，沒有不同成本：** BFS 逐層探索，先找到的路徑就具有最少邊數。
- **若每條邊代表距離或時間，且權重都非負：** 使用 Dijkstra；下一個要確定的頂點不一定是最少邊數抵達的頂點，而是目前總成本估計最小的頂點。
- **若某些邊有負成本：** Dijkstra 的貪婪確定步驟不再有保證；Bellman-Ford 透過反覆 Relaxation 讓較短距離逐步傳播，並檢查負環。

## 5. 三種演算法的流程對照

### 5.1 BFS

1. 來源點入 FIFO Queue。
2. 取出最前面的頂點。
3. 將尚未發現的鄰居設為下一層並入隊。
4. Queue 清空後完成可達頂點探索。

### 5.2 Dijkstra

1. 來源點距離設為 0，其餘設為無限大。
2. Frontier 放入 Min-Priority Queue。
3. 取出暫定距離最小的頂點並 Settled。
4. Relaxation 所有外連邊；若找到更短距離，更新 `dist`、`prev` 與優先權。
5. Queue 清空，或指定目的頂點已 Settled 時停止。

### 5.3 Bellman-Ford

1. 來源點距離設為 0，其餘設為無限大。
2. 最多進行 \(V-1\) 輪，每輪掃描所有邊並執行 Relaxation。
3. 若某一輪沒有更新，可提前停止。
4. 額外掃描所有邊，檢查是否仍有從來源點可達的邊可以改善距離；若有，表示存在來源點可達的負權重環。

三者流程的詳細說明請見各自的演算法頁面。[R2][R3][R5]

## 6. 常見情境比較

| 情境 | 建議方法 | 原因 |
|---|---|---|
| 地圖中每條道路都視為相同步數 | BFS | 最少邊數即最少成本 |
| 道路距離不同，但距離不會是負數 | Dijkstra | 可使用距離優先的貪婪策略 |
| 路徑成本包含折抵、補貼等負值 | Bellman-Ford | 可處理負權重邊 |
| 需要知道是否存在影響來源點的負環 | Bellman-Ford | 額外掃描可偵測可達負權重環 |
| 想計算所有頂點對之間的距離 | 本頁三種方法不是唯一選擇 | 可再研究 Floyd–Warshall、Johnson 等 All-pairs 方法；超出本 Wiki 的核心範圍 |

## 7. 三種演算法如何共用知識？

- **Graph 與 Weighted Graph**：三者都在圖上運作，但對邊權重的要求不同。見 [[02-graph-weighted-graph|Graph & Weighted Graph]]。
- **Shortest Path Problem**：三者都可處理某種形式的單源或單一起終點最短路徑需求。見 [[03-shortest-path-problem|Shortest Path Problem]]。
- **Edge Relaxation**：Dijkstra 和 Bellman-Ford 都使用鬆弛操作；BFS 在首次發現頂點時設定其層級距離。見 [[05-edge-relaxation|Edge Relaxation]]。
- **Priority Queue**：Dijkstra 用 Min-Priority Queue 選出最小暫定距離；BFS 使用 FIFO Queue；Bellman-Ford 逐輪掃描邊，不需要 Min-Priority Queue。見 [[06-priority-queue|Priority Queue]]。
- **Time Complexity**：資料結構會影響 Dijkstra 的複雜度；完整比較見 [[07-time-complexity|Time Complexity]]。

## 8. 常見誤解

1. **「BFS 是最短路徑演算法，所以可以取代 Dijkstra。」** 不正確。BFS 最小化邊數；Dijkstra 在非負加權圖中最小化總權重。
2. **「Dijkstra 可以處理所有加權圖。」** 不正確。它要求每條邊權重非負。
3. **「Bellman-Ford 不能有負權重環，所以完全不能處理負權重。」** 不正確。它能處理負權重邊；若有來源點可達的負權重環，則受影響頂點沒有有限最短距離，必須偵測並回報。
4. **「三種演算法複雜度可以不看實作直接比較。」** 不正確。圖表示法、Heap 實作與複雜度統計範圍都會影響結果。
5. **「找到一條路徑就代表找到最短路徑。」** 不正確。必須滿足演算法適用條件，並完成相應的探索、確定或鬆弛流程。

## 9. 參考資料

- **R2**：NetworkX Developers, *Shortest Paths*, NetworkX 3.7 documentation. <https://networkx.org/documentation/stable/reference/algorithms/shortest_paths.html>
- **R3**：Cornell University, CS 2110, *23. Shortest Paths*, Fall 2026. <https://courses.cis.cornell.edu/courses/cs2110/2026fa/lectures/lec23/>
- **R4**：Cornell University, CS 2110, *18. Heaps and Priority Queues*, Fall 2026. <https://www.cs.cornell.edu/courses/cs2110/2026fa/lectures/lec18/>
- **R5**：MIT 6.006, *Lecture 12 / Recitation 12: Bellman-Ford*. <https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/>
