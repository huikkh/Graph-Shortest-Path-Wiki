---
title: Priority Queue
aliases:
  - 優先佇列
  - Min-Priority Queue
  - Min Heap
summary: Priority Queue 依照元素的優先權決定取出順序；Dijkstra 使用 Min-Priority Queue 優先處理目前暫定距離最小的頂點。
page_type: data_structure
related_concepts:
  - Dijkstra Algorithm
  - Edge Relaxation
  - Time Complexity
  - BFS
  - Algorithm Comparison
references:
  - R4
  - R3
status: reviewed-and-ingested
---

# Priority Queue（優先佇列）

## 1. 本頁目的

本頁介紹 Priority Queue 的行為、Binary Heap 的結構，以及常見操作。讀完後，應能解釋為什麼 Dijkstra 需要 Min-Priority Queue、Min Heap 如何維持最小值位於根節點，以及插入和取出最小值時為什麼只需要沿樹高調整。

開始前，建議先閱讀 [[04-dijkstra-algorithm|Dijkstra Algorithm]]。本頁聚焦資料結構本身；Dijkstra 如何使用它，請回到該演算法頁面。

## 2. Priority Queue 是什麼？

**Priority Queue（優先佇列）** 是一種抽象資料型態（Abstract Data Type, ADT）。每個元素除了本身的資料，還會關聯一個 priority（優先權）。新增元素後，取出時不是按照加入順序，而是按照優先權決定哪個元素先被取出。

一般 FIFO Queue 是「先進先出」；Priority Queue 則是「優先權最高者先出」。例如急診室會依病患緊急程度安排處理順序，而不是單純依抵達時間排序。

### 2.1 Min-Priority Queue 與 Max-Priority Queue

- **Min-Priority Queue**：priority 數值最小的元素先被取出。
- **Max-Priority Queue**：priority 數值最大的元素先被取出。

本 Wiki 聚焦 Dijkstra，因此主要使用 **Min-Priority Queue**。每個待處理頂點的 priority 設為目前的 `dist[v]`；距離越小，就越早被取出。

> 「優先權較高」是抽象概念，不代表數字一定比較大。Dijkstra 使用的是數值越小、越優先的 Min-Priority Queue。

## 3. Priority Queue 是 ADT，Heap 是實作方式

Priority Queue 描述需要提供什麼行為；Binary Heap 則是實現這些行為的一種資料結構。兩者不能視為同義詞。

常見 Priority Queue 操作：

| 操作 | 意義 |
|---|---|
| `insert(x, p)` / `add(x, p)` | 加入元素 `x`，並指定優先權 `p` |
| `peekMin()` | 查看優先權最小的元素，但不移除 |
| `extractMin()` / `deleteMin()` | 取出並移除優先權最小的元素 |
| `isEmpty()` | 檢查佇列是否為空 |
| `size()` | 回傳目前元素數量 |

Priority Queue 也能以未排序或已排序的 List 等方式實作；不同實作會讓新增與取出操作有不同成本。這也是為什麼實際使用時需要考慮資料結構的選擇。[R4]

## 4. Binary Heap（二元堆積）

**Binary Heap** 是一種以近似完全二元樹（complete / nearly complete binary tree）為形狀的資料結構。它常用陣列儲存，並透過兩個不變量維持結構。

### 4.1 Shape Invariant（形狀不變量）

Heap 必須維持近似完全二元樹：除最後一層外，各層都填滿；最後一層的節點由左至右連續放置，不留空洞。

這個形狀限制讓樹高維持在 `O(log N)`，其中 `N` 是 Heap 內的元素數量。

### 4.2 Heap-Order Invariant（堆積順序不變量）

Min Heap 要求每個父節點的 key 都小於或等於其子節點：

\[
key(parent) \leq key(child)
\]

因此，Min Heap 的最小元素一定在根節點（root）。但除了父子關係之外，Heap 不保證左子樹的所有值都小於右子樹的所有值。

Max Heap 則採相反規則：父節點的 key 大於或等於子節點。本 Wiki 以 Min Heap 為主；Cornell 教材較完整的示範先介紹 Max Heap，再以練習引導讀者將比較規則改成 Min Heap。[R4]

### 4.3 Heap 與 Binary Search Tree 不同

Heap 只保證父節點與子節點之間的局部順序；Binary Search Tree（BST）則依左子樹、根節點、右子樹的大小關係組織資料。因此，Heap 很適合快速取得最小值，但不適合像 BST 一樣有效搜尋任意指定元素。

## 5. 用陣列表示 Binary Heap

因為 Heap 是近似完全二元樹，可以依照由上到下、每層由左到右的順序，將節點放進陣列。使用 **0-based index** 時，索引 `i` 的關係如下：

| 關係 | 陣列索引 |
|---|---|
| Parent（若 `i > 0`） | `floor((i - 1) / 2)` |
| Left child | `2i + 1` |
| Right child | `2i + 2` |

例如陣列 `[2, 5, 4, 9, 7, 8]` 對應一個合法 Min Heap：根節點 2 不大於 5 和 4；節點 5 不大於 9 和 7；節點 4 不大於 8。

## 6. Min Heap 的基本操作

### 6.1 Insert 與 Bubble Up

新增元素時，先把新元素放到陣列最後，維持近似完全二元樹的形狀。若新元素比父節點小，就與父節點交換；重複向上比較，直到抵達根節點或不再違反 Min-Heap 順序。

這個向上調整過程稱為 **Bubble Up**（也常稱 sift up）。因為最多沿著樹高移動，時間複雜度為 `O(log N)`。[R4]

### 6.2 Peek-Min

最小元素位於根節點，因此只要讀取根節點即可，不需要調整 Heap。時間複雜度為 `O(1)`。[R4]

### 6.3 Extract-Min 與 Bubble Down

取出最小元素時：

1. 記錄並移除根節點的元素。
2. 將陣列最後一個元素移到根節點。
3. 若新的根節點大於任一子節點，便與**較小的子節點**交換。
4. 持續向下調整，直到父節點不大於子節點，或已到達葉節點。

這個向下調整過程稱為 **Bubble Down**（也常稱 sift down）。最多沿著樹高移動，因此時間複雜度為 `O(log N)`。[R4]

### 6.4 操作複雜度整理

| 操作 | Binary Min Heap 的時間複雜度 | 原因 |
|---|---:|---|
| `peekMin()` | `O(1)` | 最小值在 root |
| `insert()` | `O(log N)` | 最多 Bubble Up 一條樹高 |
| `extractMin()` | `O(log N)` | 最多 Bubble Down 一條樹高 |
| `isEmpty()` | `O(1)` | 檢查元素數量是否為 0 |
| `size()` | `O(1)` | 直接讀取元素數量 |

這裡的 `N` 是目前 Heap 中元素數量。以上是單次操作成本，不等同於 Dijkstra 整體的時間複雜度；整體分析請見 [[07-time-complexity|Time Complexity]]。[R4]

## 7. Priority Queue 在 Dijkstra 中的應用

Dijkstra 每一輪都需要從 Frontier（已找到候選路徑、但最短距離尚未確定的頂點）選出 `dist` 最小者。因此，可以把每個 Frontier 頂點和它目前的 `dist[v]` 放入 Min-Priority Queue。

例如佇列目前包含：

| 頂點 | priority（暫定距離） |
|---|---:|
| B | 8 |
| C | 5 |
| D | 12 |

此時 `extractMin()` 會先取出 C，因為 5 是最小距離。

如果 Edge Relaxation 發現 B 的距離從 8 改善成 3，佇列中的 priority 也必須同步降低。這類操作通常稱為 **decrease-key** 或 `updatePriority`。[R3]

### 7.1 兩種常見更新方式

- **Indexed Heap + decrease-key**：額外維護頂點到 Heap 索引的對應，找到該頂點後降低 key，再 Bubble Up。若索引可在 `O(1)` 內找到，調整 Heap 的成本為 `O(log N)`。
- **Lazy Deletion（延遲刪除）**：若使用的 Heap 不支援原地更新，就插入新的 `(distance, vertex)` 項目；舊項目保留在佇列中，日後取出時若其距離已不是最新值便略過。這會改變佇列項目數量與整體複雜度分析，不能與 indexed decrease-key 的實作假設混用。

R3 的 Dijkstra 頁面已介紹這兩種策略的差異；本頁主要說明 Heap 結構與 priority 更新為何需要調整。[R3]

## 8. 相關概念

- [[04-dijkstra-algorithm|Dijkstra Algorithm]]：Min-Priority Queue 如何決定下一個要確定的頂點。
- [[05-edge-relaxation|Edge Relaxation]]：成功找到更短路徑後，為什麼需要更新頂點的 priority。
- [[07-time-complexity|Time Complexity]]：Heap 的單次操作成本如何影響 Dijkstra 整體效率。
- [[03-shortest-path-problem|Shortest Path Problem]]：加權最短路徑為何需要按照路徑成本選擇下一個頂點。

## 9. 參考資料

- **R4**：Cornell University, CS 2110, *18. Heaps and Priority Queues*, Fall 2026. <https://www.cs.cornell.edu/courses/cs2110/2026fa/lectures/lec18/>
- **R3**：Cornell University, CS 2110, *23. Shortest Paths*, Fall 2026. <https://courses.cis.cornell.edu/courses/cs2110/2026fa/lectures/lec23/>
