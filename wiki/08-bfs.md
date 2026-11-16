---
title: BFS
aliases:
  - Breadth-First Search
  - 廣度優先搜尋
  - 廣度優先搜索
summary: BFS 使用 FIFO Queue 逐層探索圖；在非加權圖或每條邊成本相同且非負的圖中，可求出從來源點到各可達頂點經過邊數最少的路徑。
page_type: algorithm
related_concepts:
  - Graph & Weighted Graph
  - Shortest Path Problem
  - Dijkstra Algorithm
  - Bellman-Ford
  - Priority Queue
  - Time Complexity
  - Algorithm Comparison
references:
  - R1
  - R2
  - R3
status: reviewed-and-ingested
---

# BFS（Breadth-First Search，廣度優先搜尋）

## 1. 本頁目的

本頁介紹 BFS 如何從指定來源頂點開始，一層一層探索圖中的頂點，以及它為什麼能在非加權圖中找到最少邊數的路徑。讀完後，應能理解 FIFO Queue、visited／dist／prev 的用途，並分辨 BFS 和 Dijkstra 的適用情境。

開始前，建議先閱讀 [[02-graph-weighted-graph|Graph & Weighted Graph]] 與 [[03-shortest-path-problem|Shortest Path Problem]]。

## 2. BFS 是什麼？

**Breadth-First Search（BFS，廣度優先搜尋）**是一種圖的走訪方法。從來源點開始，先拜訪距離來源點一條邊的鄰居，再拜訪距離兩條邊的頂點，接著依序往外擴展，因此稱為「廣度優先」。

BFS 使用 **FIFO Queue（先進先出佇列）**管理待處理頂點：先被發現的頂點會先被取出處理。由於頂點按照距離來源點的邊數由小到大被探索，BFS 可以在非加權圖中找到最少邊數的路徑。[R2][R3]

## 3. BFS 為什麼能找到非加權圖的最短路徑？

在非加權圖中，通常把每條邊視為成本 1。因此，路徑總成本就是經過的邊數。BFS 會依照層級探索：

- Level 0：來源點本身，距離為 0。
- Level 1：從來源點經過一條邊可以到達的頂點，距離為 1。
- Level 2：最少需要經過兩條邊才能到達的頂點，距離為 2。
- 後續層級依此類推。

FIFO Queue 保證較早發現的較小層級頂點會先被處理。當某個頂點第一次被發現時，便已經透過最少邊數的路徑抵達；因此可設定 `dist[v] = dist[u] + 1` 並記錄 `prev[v] = u`。[R2][R3]

> **適用條件：** BFS 的最短路徑保證適用於非加權圖，或每條邊具有相同非負成本的情況。若不同邊有不同權重，BFS 仍可用來走訪圖，但它最小化的是邊數，不一定是權重總和。[R2]

## 4. BFS 使用的資料

| 名稱 | 意義 |
|---|---|
| `Q` | FIFO Queue，存放已發現但尚未處理的頂點 |
| `visited[v]` | 標記頂點是否已被發現，避免重複入隊 |
| `dist[v]` | 從來源點到 `v` 的最少邊數；尚未發現時為 \(\infty\) |
| `prev[v]` | 最少邊數路徑中，`v` 的前驅頂點，用於還原路徑 |

初始化時，所有頂點設為未拜訪、`dist[v] = ∞`、`prev[v] = NIL`。接著將來源點 `s` 標記為已拜訪、`dist[s] = 0`，並放入 Queue。

## 5. BFS 執行步驟

1. 將來源點放入 FIFO Queue，並標記已拜訪。
2. 只要 Queue 不為空，就取出最前面的頂點 `u`。
3. 逐一檢查 `u` 的鄰居 `v`。
4. 若 `v` 尚未被發現，便標記 `visited[v] = true`、設定 `dist[v] = dist[u] + 1`、設定 `prev[v] = u`，再將 `v` 放入 Queue。
5. Queue 清空後，所有從來源點可達的頂點都已被探索；未被發現的頂點代表從來源點不可達。

在有向圖中，只沿著邊的箭頭方向檢查鄰居；在無向圖中，沿著無向邊可到達相鄰頂點。[R1][R2]

## 6. 執行範例

考慮以下無向、非加權圖，從 A 出發：

| 邊 | 連接關係 |
|---|---|
| A—B | A 與 B 相鄰 |
| A—C | A 與 C 相鄰 |
| B—D | B 與 D 相鄰 |
| C—E | C 與 E 相鄰 |
| D—F | D 與 F 相鄰 |
| E—F | E 與 F 相鄰 |

假設鄰居依字母順序處理，BFS 會先拜訪 A，再發現 B、C，接著發現 D、E，最後發現 F。

| 階段 | 取出的頂點 | 新發現的頂點 | 新發現頂點的距離 |
|---|---|---|---:|
| 初始化 | — | A | 0 |
| 第 1 輪 | A | B、C | 1 |
| 第 2 輪 | B | D | 2 |
| 第 3 輪 | C | E | 2 |
| 第 4 輪 | D | F | 3 |
| 第 5 輪 | E | 無 | — |
| 第 6 輪 | F | 無 | — |

因此，從 A 到 F 的最少邊數為 3。依照本例的鄰居處理順序，`prev[F] = D`，可還原路徑 A → B → D → F。若另一條等長路徑 A → C → E → F 也存在，BFS 可能因探索順序而保留不同的前驅；最短距離仍然是 3。

## 7. 虛擬碼

以下使用鄰接串列與 `visited` 陣列表示：

```text
BFS(G, source):
    for each vertex v in G:
        visited[v] = false
        dist[v] = infinity
        prev[v] = NIL

    Q = empty FIFO queue
    visited[source] = true
    dist[source] = 0
    Q.enqueue(source)

    while Q is not empty:
        u = Q.dequeue()

        for each neighbor v of u:
            if visited[v] == false:
                visited[v] = true
                dist[v] = dist[u] + 1
                prev[v] = u
                Q.enqueue(v)

    return dist, prev
```

**為什麼在入隊時就標記 visited？** 這能避免同一個頂點被多個鄰居重複加入 Queue。第一次發現時已取得最少邊數距離，不需要再次入隊更新。

## 8. 如何還原路徑？

和 Dijkstra、Bellman-Ford 一樣，BFS 可以使用 `prev` 記錄前驅。若要取得來源點 `s` 到目的點 `t` 的路徑，就從 `t` 沿著 `prev[t]`、`prev[prev[t]]` 逐步往回追蹤，直到 `s`，再反轉順序。

若 `dist[t] = ∞` 或 `visited[t] = false`，代表來源點無法到達目的點，不能還原出一條路徑。

## 9. BFS 的時間與空間複雜度

使用 **Adjacency List（鄰接串列）**時，每個頂點最多入隊、出隊一次，每條邊最多被檢查常數次，因此：

\[
T_{BFS}=O(V+E)
\]

若使用 Adjacency Matrix，檢查一個頂點的鄰居需要掃描一整列的 \(V\) 個位置，全部頂點合計為 \(O(V^2)\)。[R1][R2]

`visited`、`dist`、`prev` 與 Queue 最多使用 \(O(V)\) 輔助空間；若包含鄰接串列圖本身，總空間為 \(O(V+E)\)。完整的演算法複雜度比較請見 [[07-time-complexity|Time Complexity]]。

## 10. BFS 的限制與常見誤解

1. **BFS 不會自動考慮不同的邊權重。** 若 A→B 權重 100、A→C→B 的兩條邊權重各 1，BFS 可能選擇只有一條邊的 A→B；但加權成本 100 並不是最低成本。
2. **BFS 仍可走訪加權圖。** 限制在於它不能保證找到「總權重最低」的路徑；它保證的是邊數最少。
3. **BFS 不需要 Min-Priority Queue。** 因為層級順序已由 FIFO Queue 保證；Dijkstra 則需要依照暫定距離取出頂點，通常使用 Min-Priority Queue。
4. **未拜訪不等於距離很大但已知。** `dist[v] = ∞` 表示目前尚未從來源點找到可達路徑；若 BFS 結束後仍未拜訪，該頂點對此來源點不可達。

## 11. 相關概念

- [[02-graph-weighted-graph|Graph & Weighted Graph]]：了解頂點、邊、有向圖與無向圖。
- [[03-shortest-path-problem|Shortest Path Problem]]：了解非加權圖的最短路徑是最少邊數。
- [[04-dijkstra-algorithm|Dijkstra Algorithm]]：了解如何處理非負權重圖。
- [[06-priority-queue|Priority Queue]]：比較 FIFO Queue 與 Min-Priority Queue 的差異。
- [[07-time-complexity|Time Complexity]]：了解 BFS 的 \(O(V+E)\) 複雜度。
- [[09-bellman-ford|Bellman-Ford]]：了解可處理負權重邊的單源最短路徑演算法。
- [[10-algorithm-comparison|Algorithm Comparison]]：比較 BFS、Dijkstra、Bellman-Ford 的適用條件。

## 12. 參考資料

- **R1**：Cornell University, CS 2110, *21. Graphs*. <https://www.cs.cornell.edu/courses/cs2110/2026fa/lectures/lec21/>
- **R2**：NetworkX Developers, *Shortest Paths*, NetworkX 3.7 documentation. <https://networkx.org/documentation/stable/reference/algorithms/shortest_paths.html>
- **R3**：Cornell University, CS 2110, *23. Shortest Paths*, Fall 2026. <https://courses.cis.cornell.edu/courses/cs2110/2026fa/lectures/lec23/>
