---
title: Edge Relaxation
aliases:
  - Relaxation
  - 鬆弛操作
  - 邊鬆弛
summary: Edge Relaxation 是最短路徑演算法用來改善距離估計的基本操作：若經過一條邊能得到更小的候選距離，就更新目的頂點的 dist 與 prev。
page_type: concept
related_concepts:
  - Graph & Weighted Graph
  - Shortest Path Problem
  - Dijkstra Algorithm
  - Bellman-Ford
  - Priority Queue
  - Time Complexity
  - BFS
  - Algorithm Comparison
references:
  - R3
  - R5
status: reviewed-and-ingested
---

# Edge Relaxation（鬆弛操作）

## 1. 本頁目的

本頁介紹 **Edge Relaxation（邊鬆弛／鬆弛操作）**，也就是最短路徑演算法用來改善目前距離估計的基本步驟。讀完後，應能說明候選距離如何計算、什麼時候需要更新 `dist` 與 `prev`，以及 Dijkstra 和 Bellman-Ford 如何以不同方式安排鬆弛操作。

開始前，建議先閱讀 [[02-graph-weighted-graph|Graph & Weighted Graph]] 與 [[03-shortest-path-problem|Shortest Path Problem]]。本頁的操作會在 [[04-dijkstra-algorithm|Dijkstra Algorithm]] 和 [[09-bellman-ford|Bellman-Ford]] 中反覆使用。[R3][R5]

## 2. 什麼是 Edge Relaxation？

在最短路徑問題中，演算法會逐步找出從來源頂點 `s` 到其他頂點的較短路徑。對一條有向邊 `u → v`，若目前已知一條從 `s` 到 `u` 的路徑，就可以把這條路徑接上邊 `u → v`，得到一條從 `s` 到 `v` 的候選路徑。

**鬆弛操作**就是比較這條候選路徑與目前記錄的 `dist[v]`：如果候選路徑更短，就更新目前的距離估計。它的名稱可以理解成「放寬原本對距離的估計」，讓更好的路徑取代較差的結果。[R3][R5]

> **重要：** Edge Relaxation 是一種更新操作，不是完整的最短路徑演算法。Dijkstra 和 Bellman-Ford 都會使用它，但兩者選擇哪些邊、何時鬆弛，以及重複鬆弛的方式不同。

## 3. 鬆弛操作的公式

假設目前要檢查的有向邊為 `u → v`，邊權重為 `w(u,v)`。若 `dist[u]` 是有限值，先計算經過這條邊到達 `v` 的候選距離：

\[
 candidate = dist[u] + w(u,v)
\]

若候選距離比目前的距離估計小，就執行更新：

\[
 \text{if } candidate < dist[v]:
\]

\[
 dist[v] \leftarrow candidate
\]

\[
 prev[v] \leftarrow u
\]

其中：

| 名稱 | 意義 |
|---|---|
| `dist[u]` | 目前從來源點到 `u` 的距離估計 |
| `w(u,v)` | 邊 `u → v` 的權重 |
| `candidate` | 經過 `u → v` 到達 `v` 的候選距離 |
| `dist[v]` | 目前從來源點到 `v` 的最佳已知距離估計 |
| `prev[v]` | 目前這條最佳已知路徑中，`v` 的前驅頂點 |

### 3.1 為什麼要先檢查 `dist[u]` 是否有限？

初始化時，來源點 `s` 的距離設為 0，其餘頂點通常設為無限大：

\[
 dist[s]=0,\qquad dist[v]=\infty\quad(v\ne s)
\]

如果 `dist[u] = ∞`，代表目前還沒有找到從來源點到 `u` 的路徑，不能把它當作可用的路徑起點。因此實作時應先檢查 `dist[u]` 是否有限，再計算候選距離。這也能避免程式直接對代表不可達的無限大值進行不適當的運算。[R5]

### 3.2 為什麼使用嚴格小於？

通常只有在 `candidate < dist[v]` 時才更新，而不是在相等時也更新。若候選距離與目前距離相同，路徑成本並沒有改善，不需要更改 `prev[v]`。若存在多條同樣短的路徑，這種規則會保留其中一條已找到的最短路徑；不同的掃描順序可能讓演算法選到不同的前驅，但最短距離仍相同。

## 4. 逐步範例：找到更短的路徑

考慮以下有向加權圖，來源點為 `A`：

| 有向邊 | 權重 |
|---|---:|
| `A → B` | 7 |
| `A → C` | 2 |
| `C → B` | 3 |

初始化：

| 頂點 | `dist` | `prev` |
|---|---:|---|
| A | 0 | NIL |
| B | ∞ | NIL |
| C | ∞ | NIL |

### 第一次：鬆弛 `A → B`

\[
 candidate = dist[A] + 7 = 0 + 7 = 7
\]

因為 `7 < ∞`，所以更新：

- `dist[B] = 7`
- `prev[B] = A`

目前找到的路徑是 `A → B`，成本為 7。

### 第二次：鬆弛 `A → C`

\[
 candidate = dist[A] + 2 = 0 + 2 = 2
\]

因為 `2 < ∞`，所以更新：

- `dist[C] = 2`
- `prev[C] = A`

目前找到的路徑是 `A → C`，成本為 2。

### 第三次：鬆弛 `C → B`

\[
 candidate = dist[C] + 3 = 2 + 3 = 5
\]

目前 `dist[B] = 7`，而 `5 < 7`，因此找到更短的路徑，更新：

- `dist[B] = 5`
- `prev[B] = C`

原本的 `A → B` 成本為 7；現在改走 `A → C → B`，成本為 5。

### 最終結果

| 頂點 | 最短距離估計 | 前驅頂點 |
|---|---:|---|
| A | 0 | NIL |
| B | 5 | C |
| C | 2 | A |

因此目前找到的 `A` 到 `B` 路徑為：

\[
 A\rightarrow C\rightarrow B
\]

總成本為 `2 + 3 = 5`。這個例子展示了鬆弛操作的核心：**第一次找到一條路徑，不代表它已經是最短路徑；之後仍可能透過其他頂點找到更短的路徑。**

## 5. 鬆弛操作的虛擬碼

以下是針對單一有向邊的通用概念版程序：

```text
RELAX(u, v, weight):
    if dist[u] == infinity:
        return false

    candidate = dist[u] + weight

    if candidate < dist[v]:
        dist[v] = candidate
        prev[v] = u
        return true

    return false
```

回傳 `true` 表示距離估計有改善；回傳 `false` 表示來源頂點目前不可達，或候選距離沒有比原本更小。實際演算法可以利用這個結果，決定是否更新其他資料結構，或是否需要繼續下一輪。

> **有向圖與無向圖：** 上面的程序一次處理一個方向的邊。若圖是無向圖，無向邊 `{u,v}` 在最短路徑運算中可視為兩個方向 `u → v` 與 `v → u`，因此要分別檢查兩個方向。邊的方向與權重定義見 [[02-graph-weighted-graph|Graph & Weighted Graph]]。[R1]

## 6. Dijkstra 如何使用 Relaxation？

Dijkstra 適用於所有邊權重皆非負的圖。它不會在每一輪任意掃描整張圖的所有邊，而是先從 Min-Priority Queue 取出目前暫定距離最小的 Frontier 頂點 `u`，再檢查 `u` 的外連邊並進行鬆弛。[R3]

流程如下：

1. 從 Min-Priority Queue 取出目前距離最小且尚未 settled 的頂點 `u`。
2. 將 `u` 標記為 settled；在非負權重條件下，其最短距離已確定。
3. 逐一檢查 `u` 的外連邊 `u → v`。
4. 對每條邊執行 Relaxation。
5. 若 `dist[v]` 改善，就更新 `prev[v]`，並同步更新 Priority Queue 中 `v` 的優先權。
6. 繼續處理下一個 Frontier 頂點，直到佇列為空，或指定目的頂點已 settled。

Dijkstra 的鬆弛操作可簡化表示為：

```text
for each outgoing edge (u, v) with weight w(u, v):
    if dist[u] != infinity:
        candidate = dist[u] + w(u, v)

        if candidate < dist[v]:
            dist[v] = candidate
            prev[v] = u
            update_priority_of(v, candidate)
```

在正確的 Dijkstra 執行中，已 settled 的頂點不需要重新打開；非負權重條件保證已確定的距離不會再被改善。若實作中發現 settled 頂點仍被更短距離更新，應檢查是否有負權重邊或實作錯誤。[R3]

**相關頁面：** [[04-dijkstra-algorithm|Dijkstra Algorithm]]、[[06-priority-queue|Priority Queue]]。

## 7. Bellman-Ford 如何使用 Relaxation？

Bellman-Ford 也使用相同的鬆弛規則，但它不依照目前距離選擇下一個頂點。它會反覆掃描整個邊集合，嘗試鬆弛每一條邊，最多執行 `|V|-1` 輪；若某一輪完全沒有更新，可以提前停止。完成後還會額外檢查一次所有邊，以偵測從來源點可達的負權重環。[R5]

概念流程如下：

```text
for i = 1 to |V| - 1:
    updated = false

    for each directed edge (u, v) with weight w(u, v):
        if RELAX(u, v, w(u, v)):
            updated = true

    if updated == false:
        break

# 再掃描一次所有邊，檢查是否仍有可改善的距離。
# 若從來源點可達的邊仍能被鬆弛，代表存在可達負權重環。
```

邊的掃描順序會影響更新在同一輪內能傳播多遠。因此，一輪可能連續改善多個頂點；不能把每輪簡化成「剛好只延伸一條邊」。完整流程、`|V|-1` 輪的理由與負環條件，請參閱 [[09-bellman-ford|Bellman-Ford]]。[R5]

## 8. Dijkstra 與 Bellman-Ford 的鬆弛方式比較

| 比較項目 | Dijkstra | Bellman-Ford |
|---|---|---|
| 邊權重條件 | 必須全部非負 | 可以有負權重邊 |
| 選擇下一步的方式 | 從 Min-Priority Queue 取出距離最小的 Frontier 頂點 | 依序掃描整個邊集合 |
| 鬆弛範圍 | 處理剛 settled 頂點的外連邊 | 每輪嘗試鬆弛所有邊 |
| 重複方式 | 隨 Frontier 頂點逐步擴展 | 最多 `|V|-1` 輪，再做負環檢查 |
| 負權重環 | 不適用於含負權重邊的輸入 | 可偵測從來源點可達的負權重環 |

兩者共用相同的候選距離比較與 `dist`／`prev` 更新規則；差別在於**哪些邊會在什麼時候被鬆弛，以及演算法如何保證結果正確**。[R3][R5]

## 9. 常見誤解

1. **誤解：第一次發現頂點，就能確定它的最短距離。**
   - 正確：第一次發現只代表找到一條可行路徑；如果之後出現更小的候選距離，仍要更新 `dist` 和 `prev`。在 Dijkstra 中，必須等頂點被取出並 settled 後，距離才確定。[R3]
2. **誤解：Relaxation 就是 Dijkstra。**
   - 正確：Relaxation 是可被不同最短路徑演算法使用的基本操作。Dijkstra 與 Bellman-Ford 的邊選擇和重複策略不同。[R3][R5]
3. **誤解：只要候選距離有限，就一定要更新。**
   - 正確：只有候選距離嚴格小於目前 `dist[v]` 時才更新；相等不代表找到更短路徑。
4. **誤解：Bellman-Ford 每輪只會更新一條邊。**
   - 正確：每輪掃描整個邊集合，更新可能依掃描順序在同一輪內連續傳播。[R5]
5. **誤解：有負權重邊就不能做 Relaxation。**
   - 正確：鬆弛操作本身可以比較負權重邊所形成的候選距離；是否能正確求解，取決於使用的演算法及圖中是否有來源點可達的負權重環。[R5]

## 10. 相關概念

- [[02-graph-weighted-graph|Graph & Weighted Graph]]：理解有向邊、無向邊與邊權重。
- [[03-shortest-path-problem|Shortest Path Problem]]：理解路徑成本與單源最短路徑。
- [[04-dijkstra-algorithm|Dijkstra Algorithm]]：理解 Dijkstra 如何對 Frontier 頂點的外連邊進行鬆弛。
- [[06-priority-queue|Priority Queue]]：理解 Dijkstra 在距離改善後如何更新頂點優先權。
- [[09-bellman-ford|Bellman-Ford]]：理解重複鬆弛與負權重環偵測。
- [[07-time-complexity|Time Complexity]]：比較使用不同演算法時的整體執行成本。

## 11. 參考資料

- **[R3]** Cornell University, CS 2110, *23. Shortest Paths*, Fall 2026. <https://courses.cis.cornell.edu/courses/cs2110/2026fa/lectures/lec23/>
- **[R5]** MIT OpenCourseWare, 6.006 *Introduction to Algorithms*, *Lecture 12: Bellman-Ford* and *Recitation 12: Bellman-Ford*, Spring 2020. <https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/2430d7903a5529451d80c17f89a41fe8_MIT6_006S20_lec12.pdf> and <https://live.ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/1a34924a2ee4e08c580c2268f8e854ee_MIT6_006S20_r12.pdf>
