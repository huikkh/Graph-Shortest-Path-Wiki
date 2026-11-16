---
title: Time Complexity
aliases:
  - 時間複雜度
  - 空間複雜度
  - Complexity Analysis
summary: Time Complexity 描述演算法執行步驟如何隨頂點數 V 與邊數 E 增長；本頁比較 BFS、Dijkstra、Bellman-Ford 及常見圖表示法的時間與空間成本。
page_type: analysis
related_concepts:
  - Graph & Weighted Graph
  - Shortest Path Problem
  - BFS
  - Dijkstra Algorithm
  - Bellman-Ford
  - Priority Queue
  - Edge Relaxation
  - Algorithm Comparison
references:
  - R1
  - R2
  - R3
  - R4
  - R5
status: reviewed-and-ingested
---

# Time Complexity（時間與空間複雜度）

## 1. 本頁目的

本頁說明如何分析最短路徑演算法的執行效率，並比較 BFS、Dijkstra 與 Bellman-Ford。讀完後，應能看懂符號 \(V\)、\(E\)、\(O(\cdot)\)，知道圖的儲存方式與 Priority Queue 實作為何會影響效率，也能區分「輔助空間」與「包含圖本身的總儲存空間」。

開始前，建議先閱讀 [[02-graph-weighted-graph|Graph & Weighted Graph]]、[[04-dijkstra-algorithm|Dijkstra Algorithm]]、[[06-priority-queue|Priority Queue]]、[[08-bfs|BFS]] 與 [[09-bellman-ford|Bellman-Ford]]。

## 2. 複雜度分析是什麼？

**Time Complexity（時間複雜度）**描述輸入規模增加時，演算法執行操作數量的增長趨勢。它通常不直接代表實際執行秒數，因為實際時間也會受硬體、程式語言、實作細節與資料分布影響。

本 Wiki 使用 Big-O（大 O）表示漸進上界，重點是比較輸入規模變大時，演算法成本如何成長，而不是計算某台電腦上精確需要幾秒。

### 2.1 圖論常用符號

| 符號 | 意義 |
|---|---|
| \(V\) | 頂點（Vertex）的數量 |
| \(E\) | 邊（Edge）的數量 |
| \(N\) | 在 Heap 頁面中，代表目前 Heap 裡的元素數量；它不一定等於圖的頂點數 \(V\) |

圖的稀疏程度也會影響演算法成本：**Sparse Graph（稀疏圖）**的邊數相對少；**Dense Graph（稠密圖）**的邊數接近頂點數平方的量級。對簡單圖而言，邊數通常不會超過 \(O(V^2)\)。[R1][R2]

## 3. 圖的表示法與空間成本

圖的表示方式會影響儲存空間，也會影響遍歷鄰居的成本。[R1]

| 表示方式 | 儲存方式 | 空間複雜度 | 適合情境 |
|---|---|---:|---|
| Adjacency List（鄰接串列） | 每個頂點保存自己的鄰居 | \(O(V+E)\) | 稀疏圖；需要逐一走訪鄰居 |
| Adjacency Matrix（鄰接矩陣） | 使用 \(V\times V\) 矩陣記錄頂點間是否有邊或邊權重 | \(O(V^2)\) | 頂點數較少或圖較稠密；快速查詢兩頂點間是否直接相連 |

在鄰接串列中，遍歷某個頂點的所有鄰居，成本與該頂點的 degree（度數）成正比；若整張圖的每個頂點與邊都只走訪常數次，總成本通常是 \(O(V+E)\)。

> **空間複雜度的口徑：** 若把圖本身也計入，鄰接串列需要 \(O(V+E)\)，鄰接矩陣需要 \(O(V^2)\)。若只看演算法額外使用的陣列、Queue、Heap 等輔助資料結構，則應另外計算，不能把兩種口徑混在一起。

## 4. BFS 的時間與空間複雜度

BFS 使用 FIFO Queue，從來源點開始逐層探索。使用鄰接串列時，每個頂點最多入隊一次、出隊一次，每條邊也只會被檢查常數次，因此：

\[
T_{BFS}=O(V+E)
\]

若使用鄰接矩陣，為了檢查一個頂點的鄰居，可能需要掃描該列的全部 \(V\) 個位置；對所有頂點執行時，時間複雜度為：

\[
T_{BFS,matrix}=O(V^2)
\]

BFS 的 `visited`／`dist`／`prev` 陣列與 Queue 最多保存 \(O(V)\) 個頂點，因此輔助空間為 \(O(V)\)，不包含圖本身。[R2][R3]

## 5. Dijkstra 的時間與空間複雜度

Dijkstra 每輪需要取出目前暫定距離最小的 Frontier 頂點，並在成功 Relaxation 時更新鄰居的距離。因此，整體成本會受到「如何找到最小頂點」以及「如何更新優先權」影響。[R3][R4]

### 5.1 鄰接串列 + Binary Min Heap

若使用支援 `decrease_key` 的 Indexed Binary Min Heap：

- 最多有 \(V\) 個頂點被 `extractMin`，每次成本 \(O(\log V)\)。
- 每條邊最多造成常數次鬆弛檢查；若成功更新，`decrease_key` 成本為 \(O(\log V)\)。
- 加總後常用上界為：

\[
T_{Dijkstra}=O((V+E)\log V)
\]

在連通圖或可省略孤立頂點項的常見情況下，也常簡寫為 \(O(E\log V)\)。本頁保留較一般的 \(O((V+E)\log V)\) 形式。[R2][R3][R4]

### 5.2 線性掃描或鄰接矩陣

若每輪以線性掃描找出最小暫定距離，選取頂點的成本總計可達 \(O(V^2)\)。若再使用鄰接矩陣掃描鄰居，常見總時間為：

\[
T_{Dijkstra,array/matrix}=O(V^2)
\]

因此，對稠密圖，簡單陣列／矩陣版本有時不會比 Heap 版本差；不能只看演算法名稱就斷言某一種實作永遠較快。[R2]

### 5.3 Lazy Deletion 的注意事項

若 Heap 不支援原地 `decrease_key`，可以在距離改善時新增一筆較新的 `(distance, vertex)`，等舊項目被取出時再判斷它是否過期。這種 Lazy Deletion 會讓 Heap 中可能同時存在同一頂點的多筆項目，操作次數與 Heap 最大項目數必須依實際實作分析，不能直接把 Indexed Heap 的操作計數原封不動套用。[R3]

### 5.4 Dijkstra 的空間複雜度

鄰接串列圖本身需要 \(O(V+E)\)；`dist`、`prev`、settled 狀態與 Indexed Priority Queue 通常各需要 \(O(V)\) 等級的空間。因此包含圖本身的總空間通常為 \(O(V+E)\)，額外演算法空間為 \(O(V)\)。[R3]

## 6. Bellman-Ford 的時間與空間複雜度

Bellman-Ford 最多進行 \(V-1\) 輪，每輪掃描所有 \(E\) 條邊；最後再掃描一次邊集合檢查是否仍可改善距離。因此最壞時間複雜度為：

\[
T_{Bellman-Ford}=O(VE)
\]

若某一輪完全沒有距離更新，可以提前停止，但最壞情況仍是 \(O(VE)\)。[R5]

Bellman-Ford 的 `dist` 與 `prev` 陣列使用 \(O(V)\) 輔助空間；若把鄰接串列形式的圖也計入，總空間為 \(O(V+E)\)。[R5]

## 7. 三種演算法複雜度總表

下表採用常見的鄰接串列表示法；Dijkstra 欄位假設使用 Binary Min Heap。空間欄同時區分輔助空間與圖本身的儲存成本。[R2][R3][R4][R5]

| 演算法 | 主要資料結構／策略 | 時間複雜度 | 輔助空間（不含圖） | 圖儲存空間 |
|---|---|---:|---:|---:|
| BFS | FIFO Queue | \(O(V+E)\) | \(O(V)\) | \(O(V+E)\) |
| Dijkstra | Indexed Binary Min Heap | \(O((V+E)\log V)\) | \(O(V)\) | \(O(V+E)\) |
| Bellman-Ford | 重複掃描邊集合 | \(O(VE)\) | \(O(V)\) | \(O(V+E)\) |

## 8. 如何直觀理解成長速度？

以下只作為簡化直覺，並假設使用鄰接串列及常見實作：

- 當圖很稀疏，\(E\) 與 \(V\) 同一量級時，BFS 約為 \(O(V)\)，Dijkstra Binary Heap 約為 \(O(V\log V)\)，Bellman-Ford 約為 \(O(V^2)\)。
- 當圖很稠密，\(E\) 接近 \(V^2\) 時，BFS 約為 \(O(V^2)\)，Dijkstra Binary Heap 約為 \(O(V^2\log V)\)，Bellman-Ford 約為 \(O(V^3)\)。此時 Dijkstra 的線性掃描／鄰接矩陣版本也可能達到 \(O(V^2)\)。

這些是漸進複雜度比較，不代表小型輸入時的實際執行秒數；資料結構常數、語言與實作都可能影響實際表現。

## 9. 常見誤解

1. **把 Heap 單次操作的複雜度當成 Dijkstra 整體複雜度。** `extractMin` 是單次 \(O(\log V)\)，但 Dijkstra 還會處理所有邊與距離更新。
2. **不說明圖表示法就給複雜度。** 鄰接矩陣與鄰接串列的空間、鄰居掃描成本不同。
3. **把輔助空間和總空間混為一談。** `dist` 等陣列是額外空間；圖本身也占有儲存空間。
4. **把所有 Dijkstra 實作視為相同。** Indexed Heap、Lazy Deletion、線性掃描使用不同的操作次數與資料結構假設。
5. **把 Big-O 當作實際秒數。** Big-O 描述輸入規模成長時的漸進趨勢，不直接給出某台電腦的執行時間。

## 10. 相關概念

- [[02-graph-weighted-graph|Graph & Weighted Graph]]：了解 \(V\)、\(E\) 與圖的表示法。
- [[04-dijkstra-algorithm|Dijkstra Algorithm]]：理解最短距離演算法如何使用 Frontier 與 Priority Queue。
- [[05-edge-relaxation|Edge Relaxation]]：理解每條邊如何嘗試改善距離。
- [[06-priority-queue|Priority Queue]]：理解 Binary Heap 單次操作成本。
- [[08-bfs|BFS]]：了解線性時間的無權重最短路徑方法。
- [[09-bellman-ford|Bellman-Ford]]：了解重複鬆弛的成本。
- [[10-algorithm-comparison|Algorithm Comparison]]：比較三種演算法的適用條件與效率。

## 11. 參考資料

- **R1**：Cornell University, CS 2110, *21. Graphs*. <https://www.cs.cornell.edu/courses/cs2110/2026fa/lectures/lec21/>
- **R2**：NetworkX Developers, *Shortest Paths*, NetworkX 3.7 documentation. <https://networkx.org/documentation/stable/reference/algorithms/shortest_paths.html>
- **R3**：Cornell University, CS 2110, *23. Shortest Paths*, Fall 2026. <https://courses.cis.cornell.edu/courses/cs2110/2026fa/lectures/lec23/>
- **R4**：Cornell University, CS 2110, *18. Heaps and Priority Queues*, Fall 2026. <https://www.cs.cornell.edu/courses/cs2110/2026fa/lectures/lec18/>
- **R5**：MIT 6.006, *Lecture 12 / Recitation 12: Bellman-Ford*. <https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/>
