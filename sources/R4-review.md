---
source_id: R4
institution: Cornell University
course: CS 2110
source_title: 18. Heaps and Priority Queues
term: 2026 Fall
source_type: university_course_lecture_notes
url: https://www.cs.cornell.edu/courses/cs2110/2026fa/lectures/lec18/
proposed_used_by:
  - Priority Queue
  - Dijkstra Algorithm
  - Time Complexity
review_status: approved
ingest_status: ingested
---

# R4 Review — Cornell CS 2110: 18. Heaps and Priority Queues

## 1. 來源選擇與背景

原先來源規劃曾列 Princeton Algorithms 4th Edition — Priority Queues（`https://algs4.cs.princeton.edu/24pq/`）。本次查閱時該頁回應 HTTP 403，無法可靠檢視完整教材內容。因此本次改以 Cornell University CS 2110 Fall 2026 Lecture 18 — **Heaps and Priority Queues** 作為 R4 的主要候選來源。這是來源規劃的調整，不代表 Princeton 教材內容有誤。

Cornell 教材完整介紹 Priority Queue ADT、List-based 實作、Heap 的形狀與順序不變量、陣列表示、`add` / `remove`、`peek`，並以 Min Heap 練習說明如何將 max-heap 的不變量改成 min-heap。教材亦明確指出 Dijkstra 的有效率實作需要支援佇列項目的 priority update。

原始來源：<https://www.cs.cornell.edu/courses/cs2110/2026fa/lectures/lec18/>

## 2. 建議擷取範圍

### 納入 Priority Queue 頁面（06）

- **Priority Queue 是 ADT（抽象資料型態）**：依照 priority 而非加入順序取出元素。要區分 ADT 的行為規格與 Heap 這種實作方式。
- **Min-Priority Queue**：在 Dijkstra 中，以目前暫定距離作為 priority；距離數值越小，越早被取出。避免將「較高 priority」誤解成「數字較大」。
- **Binary Heap 的兩個不變量**：
  - Shape invariant：近似／完整二元樹（complete / nearly complete binary tree），除最底層外各層填滿，最底層由左向右填入。
  - Min-heap order invariant：每個節點的 key 不大於其子節點，因此最小元素位於根節點。
- **陣列表示法**：以 0-based index 儲存層序走訪的元素；`parent(i) = floor((i-1)/2)`、`left(i) = 2i+1`、`right(i) = 2i+2`。
- **核心操作**：
  - `insert` / `add`：新元素放在陣列尾端，再透過 `bubbleUp` 修復 heap order。
  - `peekMin`：讀取根節點，不移除。
  - `extractMin` / `deleteMin`：根與最後一個元素交換、移除尾端，再透過 `bubbleDown` 修復 heap order；向下調整時選擇較小的子節點。
  - `isEmpty`、`size`。
- **Dijkstra 所需的 priority 更新**：當找到更短路徑時，需要降低該頂點的 priority（常稱 `decrease_key` 或 `updatePriority`）。本來源說明 Dijkstra 需要 priority updates，但沒有完整展開索引式 heap 的 update 實作；操作語意與 Dijkstra 使用情境應連回 R3，不要宣稱 R4 單獨提供完整 decrease-key 實作。

### 可作為補充，不必深入

- List-based priority queue 可用來說明為何單純 List 雖容易實作，但 `peek` / `remove` 可能需要線性掃描。
- Max Heap 與 Min Heap 的差別可用來幫助理解；本 Wiki 聚焦 Dijkstra，因此正文以 Min Heap 為主。
- Heap 的 array representation 與 near-complete shape 可保留簡單圖示和 index 範例。

### 不建議匯入

- Heapsort 的完整內容。
- Max Heap 全套程式碼及 Java 語法細節。
- Median Queue、Order Statistics 等與本 Wiki 教學目標關聯較低的延伸題。
- 將教材中的 Max-Priority Queue 例子直接照搬成 Dijkstra 的優先規則；本 Wiki 必須明確切換成 Min-Priority Queue。

## 3. 與現有 Wiki 的重複內容比對

| 概念 | 目前 Dijkstra 頁面（04） | R4 建議處理 |
|---|---|---|
| 為何需要 Min-Priority Queue | 已說明用途 | 不重複長篇解釋；06 說明 ADT 與操作原理 |
| `insert` / `extract_min` | 只列出演算法所需操作 | 06 詳細解釋操作 |
| `decrease_key` / `updatePriority` | 已有操作語意與使用情境 | 06 解釋為何需要降低 priority；完整索引更新細節若來源不足則不虛構 |
| Lazy Deletion | 04 已有概念補充 | 06 僅用交叉連結，不將 lazy deletion 誤寫成 Binary Heap 本身的必要功能 |
| Heap 的結構與不變量 | 尚未詳細介紹 | 06 新增 |
| `bubbleUp` / `bubbleDown` | 尚未詳細介紹 | 06 新增 |
| PQ 操作複雜度 | 04 有 Dijkstra 整體複雜度摘要 | 06 列出單次操作成本；07 再統整整體演算法複雜度 |

## 4. 技術正確性與教學注意事項

1. **ADT 不等於 Heap**：Priority Queue 描述「依優先權取出元素」的行為；Binary Heap 是常見實作之一。也能用 List、平衡搜尋樹等方式實作，但操作成本不同。
2. **Min-Heap 的比較方向**：Min-Heap 要求 parent key ≤ child key；最小 key 位於 root。不要照抄 Cornell Max-Heap 範例的比較符號而未反轉。
3. **Shape invariant 與 order invariant 不可混為一談**：complete shape 保證樹高為 `O(log N)`；heap-order 保證根節點為最小值。兩者一起維持才能得到預期效能。
4. **Heap 不是 Binary Search Tree**：Heap 只保證父子之間的局部順序，不保證左子樹所有值小於右子樹，也不適合直接用 BST 的搜尋方式搜尋任意元素。
5. **複雜度需對應操作**：Binary Heap 的 `peekMin` 為 `O(1)`；`insert` 與 `extractMin` 為 `O(log N)`；`isEmpty` / `size` 通常為 `O(1)`。此處是單次操作成本，不等同 Dijkstra 整體複雜度。
6. **Priority update 需說明實作前提**：如果使用 indexed heap，可透過索引定位元素並執行 `decrease_key`，修復成本通常為 `O(log N)`；若使用不支援原地更新的標準 heap，也可採用重複插入新 `(distance, vertex)` 項目並在取出時忽略過期項目（lazy deletion）。兩種方式應分開說明，不能在同一段虛擬碼混用假設。
7. **一般 FIFO Queue 不適用於 Dijkstra 的加權 frontier 選擇**：FIFO 按抵達順序取出；Dijkstra 必須按目前距離最小值取出。這是 Queue 與 Priority Queue 的關鍵差異。
8. **教材呈現 Max Heap 較完整、Min Heap 作為練習**：對初學者頁面應清楚聲明本 Wiki 將其對稱地改寫為 Min Heap，並核對所有比較方向；不要把教材的 Max Heap 程式碼直接當成 Min Heap。

## 5. 與其他 Wiki 頁面的關聯

- `wiki/04-dijkstra-algorithm.md`：說明 Dijkstra 如何使用 Min-Priority Queue，及 `decrease_key` / lazy deletion 的實作分支。
- `wiki/07-time-complexity.md`：整合 Binary Heap 單次操作成本與 Dijkstra 的整體複雜度。
- `wiki/03-shortest-path-problem.md`：說明加權最短路徑為何需要依距離選取下一個頂點。
- `wiki/05-edge-relaxation.md`：說明鬆弛成功後為何要更新 queue 中對應的 priority。

## 6. 建議的 Ingest 範圍與目標頁面

- 主要頁面：`wiki/06-priority-queue.md`
- 交叉更新：`wiki/04-dijkstra-algorithm.md`（確認連結與術語一致；除非有必要，不重寫既有內容）
- 其他連結：`wiki/03-shortest-path-problem.md`、`wiki/05-edge-relaxation.md`、`wiki/07-time-complexity.md`
- 建議 references：`R4`；Dijkstra priority update 的跨頁說明同時引用 `R3`。

## 7. Review 結論

**Review 已由使用者核准，並已正式 Ingest。**

理由：Cornell CS 2110 官方課程教材有系統地說明 Priority Queue ADT、Heap 的形狀與順序不變量、陣列表示、插入／移除操作與複雜度，並指出 Dijkstra 需要 priority update。這些內容可填補目前 04 頁只介紹 PQ 用途、尚未解釋 Min-Heap 內部原理的缺口。

來源規劃調整：原訂 Princeton Algorithms 4th Edition 頁面在本次查閱時回應 HTTP 403，故改以 Cornell CS 2110 2026 Fall Lecture 18 作為 R4 主要來源候選。這是可讀取性與教材完整度考量，不代表 Princeton 教材不可靠。

### 審查檢查表

- [x] 來源為 Cornell CS 2110 官方課程教材。
- [x] 已確認 ADT 與 Heap 實作的區別。
- [x] 已確認 Priority Queue、Min-Heap、`bubbleUp`、`bubbleDown` 的內容範圍。
- [x] 已辨識與 Dijkstra 頁面重複之處，避免重複定義。
- [x] 已區分 `decrease_key` 與 lazy deletion，不混用實作假設。
- [x] 已提醒 Cornell 教材主文以 Max Heap 為例，Wiki 必須正確轉成 Min Heap。
- [x] 使用者核准（2026-11-16）。
- [x] 正式 Ingest：`wiki/06-priority-queue.md` 已建立。
