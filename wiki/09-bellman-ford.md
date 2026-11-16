---
title: Bellman-Ford
aliases:
  - Bellman-Ford Algorithm
  - 貝爾曼－福特演算法
  - 貝爾曼福特演算法
summary: Bellman-Ford 使用重複的 Edge Relaxation 求解單源最短路徑，可處理負權重邊，並能偵測從來源點可達的負權重環。
page_type: algorithm
related_concepts:
  - Graph & Weighted Graph
  - Shortest Path Problem
  - Dijkstra Algorithm
  - Edge Relaxation
  - Time Complexity
  - BFS
  - Algorithm Comparison
  - Priority Queue
references:
  - R1
  - R2
  - R3
  - R5
status: reviewed-and-ingested
---

# Bellman-Ford（貝爾曼－福特演算法）

## 1. 本頁目的

本頁介紹 Bellman-Ford 如何解決**單源最短路徑（Single-Source Shortest Paths, SSSP）**問題，以及它如何處理負權重邊、偵測負權重環。讀完後，應能說明為什麼演算法最多需要進行 \(|V|-1\) 輪 Edge Relaxation、額外檢查輪的用途，以及它和 Dijkstra 的差異。

開始前，建議先閱讀 [[02-graph-weighted-graph|Graph & Weighted Graph]] 與 [[03-shortest-path-problem|Shortest Path Problem]]。本頁會使用 [[05-edge-relaxation|Edge Relaxation]] 的概念；該頁完成後可再閱讀其一般化說明。

## 2. Bellman-Ford 要解決什麼問題？

**Bellman-Ford Algorithm** 用來計算指定來源頂點 \(s\) 到其他可達頂點的最短距離。它與 Dijkstra 一樣屬於單源最短路徑演算法，但一個重要差異是：**Bellman-Ford 可以處理負權重邊**。

| 演算法 | 邊權重條件 | 主要策略 |
|---|---|---|
| BFS | 通常用於每條邊成本相同的非加權圖 | 逐層探索，找出邊數最少的路徑 |
| Dijkstra | 所有邊權重皆須非負 | 每輪確定目前暫定距離最小的頂點 |
| Bellman-Ford | 可以有負權重邊，但必須檢查來源點可達的負權重環 | 重複掃描所有邊並嘗試鬆弛 |

上述條件與演算法選擇可回看 [[03-shortest-path-problem|Shortest Path Problem]] 及 [[10-algorithm-comparison|Algorithm Comparison]]。[R2][R3][R5]

> **重要：** Bellman-Ford 可以處理負權重邊，不代表它能在所有負權重環存在時都輸出有限的最短距離。如果來源點能到達負權重環，且該環能繼續到達某些頂點，這些頂點的路徑成本可以無限下降。

## 3. 負權重邊與負權重環

### 3.1 Negative Weight（負權重）

負權重邊是權重小於 0 的邊。例如某條邊的權重為 \(-4\)，表示走過這條邊會讓路徑總成本減少 4。

**有負權重邊，不代表一定有負權重環。** 只要圖中沒有影響該來源點最短路徑的負權重環，Bellman-Ford 仍可計算有限的最短距離。[R5]

### 3.2 Negative-Weight Cycle（負權重環）

負權重環是沿著一個環走完一圈後，所有邊權重總和小於 0 的環。例如：

\[
A\rightarrow B\rightarrow C\rightarrow A
\]

若三條邊的權重分別為 \(-2\)、\(1\)、\(0\)，則整個環的總權重為：

\[
-2+1+0=-1
\]

每多繞行一次，路徑成本就再減少 1。因此，只要這個負權重環能從來源點到達，並且能從環繼續到達某個頂點，該頂點的路徑成本就可以不斷下降，不存在有限的最短距離；常以 \(-\infty\) 表示。[R5]

### 3.3 負權重環的可達性很重要

對單源最短路徑問題而言，不能只因為圖中某處存在負權重環，就說所有頂點都受到影響。需要同時考慮：

1. 來源點 \(s\) 是否能到達該負權重環？
2. 從該負權重環是否能繼續到達目標頂點？

若負環無法從來源點到達，它不會影響這次以 \(s\) 為來源的最短路徑結果。若負環可由來源點到達，只有該負環本身及能從該環繼續到達的頂點，才會受到成本無限下降的影響。其他可達但無法由負環抵達的頂點，仍可能有有限的最短距離。[R5]

> **無向圖注意事項：** 若無向邊的權重為負，沿該邊走到另一端再走回來，便形成總權重為負的封閉路徑。因此，只要來源點能到達這條負權重無向邊，就會遇到負權重環的問題。討論負權重邊時，必須留意圖是有向還是無向。[R5]

## 4. 核心資料與初始化

Bellman-Ford 使用距離估計與前驅紀錄：

| 名稱 | 意義 |
|---|---|
| `dist[v]` | 目前找到的來源點 \(s\) 到頂點 \(v\) 的最短距離估計；尚未找到路徑時為 \(\infty\) |
| `prev[v]` | 目前最佳路徑中，頂點 \(v\) 的前一個頂點，用於還原路徑 |
| `E` | 圖中的邊集合；每一輪都會逐一檢查這些邊 |

初始化方式：

\[
 dist[s]=0,\qquad dist[v]=\infty\quad(v\ne s)
\]

所有 `prev[v]` 先設為 `NIL`。只有當來源點到某個頂點已有有限距離估計時，才可用它作為鬆弛邊的起點；實作時必須跳過 `dist[u] = ∞` 的頂點，避免把不可達狀態拿來做加法。[R5]

## 5. Edge Relaxation（鬆弛操作）

Bellman-Ford 的核心是反覆檢查每一條邊。對一條有向邊 \((u,v)\)，權重為 \(w(u,v)\)，若目前從來源點到 \(u\) 的距離是有限值，就計算經過這條邊到達 \(v\) 的候選距離：

\[
 candidate=dist[u]+w(u,v)
\]

如果候選距離比目前的 `dist[v]` 更小，就更新：

\[
 dist[v]\leftarrow candidate,\qquad prev[v]\leftarrow u
\]

這個過程稱為 **Relaxation（鬆弛）**：當找到更便宜的路徑時，就降低目前的距離估計。完整的一般概念會整理在 [[05-edge-relaxation|Edge Relaxation]]；Dijkstra 也會使用相同的基本更新條件，但選擇邊與更新的控制流程不同。[R3][R5]

## 6. 為什麼需要最多 \(|V|-1\) 輪？

假設圖有 \(|V|\) 個頂點，而且不存在從來源點可達、會影響目標的負權重環。此時有限最短路徑可以選成一條 **Simple Path（簡單路徑）**，也就是不重複經過同一個頂點的路徑。

一條不重複頂點的路徑，最多只能經過 \(|V|\) 個頂點，因此最多包含 \(|V|-1\) 條邊。Bellman-Ford 每一輪都掃描所有邊，反覆嘗試改善距離；經過最多 \(|V|-1\) 輪，就足以涵蓋有限最短簡單路徑所需的邊數。[R5]

**邊的掃描順序可能影響一輪內能傳播多少更新。** 若某條路徑的邊剛好依路徑順序被掃描，更新可能在同一輪連續傳播多條邊；若掃描順序相反，資訊可能要跨多輪才傳到路徑末端。因此，不應把每一輪描述成「剛好只處理一條邊長的路徑」。標準演算法仍保證最多 \(|V|-1\) 輪足夠。[R5]

## 7. 演算法執行步驟

1. **初始化：** 將所有頂點的 `dist` 設為 \(\infty\)，`prev` 設為 `NIL`，再將來源點的 `dist` 設為 0。
2. **重複鬆弛：** 最多執行 \(|V|-1\) 輪；每輪依序檢查圖中的每一條邊。
3. **更新距離：** 若邊的起點可從來源點到達，且 `dist[u] + w(u,v) < dist[v]`，就更新 `dist[v]` 和 `prev[v]`。
4. **提前停止：** 如果某一整輪沒有任何距離更新，代表距離估計已收斂，可以提前結束，不必執行剩餘輪次。
5. **負環檢查：** 完成鬆弛後，再掃描所有邊一次。如果仍有從來源點可達的邊可以讓距離變得更小，表示存在從來源點可達的負權重環。
6. **回傳結果：** 若沒有偵測到相關負權重環，就回傳 `dist` 與 `prev`；若偵測到，則必須標示負環問題，不能把受影響頂點的距離當成有限的最短距離。[R5]

## 8. 執行範例：包含負權重邊，但沒有負權重環

考慮以下有向圖：

| 有向邊 | 權重 |
|---|---:|
| \(S\rightarrow A\) | 2 |
| \(A\rightarrow B\) | -5 |
| \(B\rightarrow C\) | 2 |
| \(C\rightarrow D\) | 1 |

從來源點 \(S\) 出發。這張圖有一條負權重邊，但沒有任何環，因此不存在負權重環。為了清楚展示每輪的效果，假設掃描邊的順序為：\(C\rightarrow D\)、\(B\rightarrow C\)、\(A\rightarrow B\)、\(S\rightarrow A\)。

圖中共有 5 個頂點，因此最多需要 \(5-1=4\) 輪。由於邊是以路徑的反方向順序掃描，更新會逐輪向前傳播：

| 階段 | 本輪成功更新 | 更新後距離（S, A, B, C, D） |
|---|---|---|
| 初始化 | `dist[S]=0` | (0, ∞, ∞, ∞, ∞) |
| 第 1 輪 | \(S\rightarrow A\)：A = 2 | (0, 2, ∞, ∞, ∞) |
| 第 2 輪 | \(A\rightarrow B\)：B = 2 + (-5) = -3 | (0, 2, -3, ∞, ∞) |
| 第 3 輪 | \(B\rightarrow C\)：C = -3 + 2 = -1 | (0, 2, -3, -1, ∞) |
| 第 4 輪 | \(C\rightarrow D\)：D = -1 + 1 = 0 | (0, 2, -3, -1, 0) |

再額外檢查所有邊，沒有任何邊能繼續改善距離，因此沒有偵測到從 \(S\) 可達的負權重環。

最終結果：

| 頂點 | 最短距離 | 前驅頂點 |
|---|---:|---|
| S | 0 | — |
| A | 2 | S |
| B | -3 | A |
| C | -1 | B |
| D | 0 | C |

從 S 到 D 的最短路徑為：

\[
S\rightarrow A\rightarrow B\rightarrow C\rightarrow D
\]

總成本為：

\[
2+(-5)+2+1=0
\]

這個例子說明：路徑上可以有負權重邊，總路徑成本也可能小於先前某個頂點的距離；只要沒有影響結果的負權重環，Bellman-Ford 仍能找到正確的有限最短距離。[R5]

## 9. 為什麼 Dijkstra 不適合處理負權重邊？

Dijkstra 會把目前暫定距離最小的頂點標記為已確定（Settled），這個貪婪選擇依賴所有邊權重非負。若之後可能經過負權重邊，已確定的距離就可能再被降低。

例如：

- \(S\rightarrow A\) 權重為 2。
- \(S\rightarrow B\) 權重為 5。
- \(B\rightarrow A\) 權重為 -10。

Dijkstra 可能先以距離 2 確定 A；但實際上路徑 \(S\rightarrow B\rightarrow A\) 的成本是 \(5+(-10)=-5\)，更短。Bellman-Ford 透過重複鬆弛所有邊，不會依照 Dijkstra 的方式過早固定 A 的距離，因此可以處理這類負權重邊。[R3][R5]

## 10. 虛擬碼

以下是標準 Bellman-Ford 的概念版虛擬碼。它假設圖以邊集合 `E` 表示，並使用**原地（in-place）鬆弛**：一輪中更新的距離可供同一輪稍後掃描到的邊使用。

```text
BELLMAN_FORD(G, source):
    for each vertex v in G:
        dist[v] = infinity
        prev[v] = NIL

    dist[source] = 0

    for i = 1 to |V| - 1:
        updated = false

        for each directed edge (u, v) with weight w(u, v) in E:
            if dist[u] != infinity:
                candidate = dist[u] + w(u, v)

                if candidate < dist[v]:
                    dist[v] = candidate
                    prev[v] = u
                    updated = true

        if updated == false:
            break

    for each directed edge (u, v) with weight w(u, v) in E:
        if dist[u] != infinity and dist[u] + w(u, v) < dist[v]:
            return "reachable negative-weight cycle detected"

    return dist, prev
```

注意：最後的檢查輪是用來偵測問題，不是再把距離當作一般有限最短路徑結果繼續回傳。這個基本版本回報「存在從來源點可達的負權重環」；若要列出負環本身，或標記所有會受到負環影響的頂點，還需要額外的前驅追蹤與可達性擴展程序，這不屬於本頁的初學者主流程。[R5]

## 11. 時間與空間複雜度

令 \(|V|\) 為頂點數、\(|E|\) 為邊數：

- 最多進行 \(|V|-1\) 輪，每輪掃描所有 \(|E|\) 條邊。
- 額外負環檢查再掃描一次所有邊。
- 因此標準最壞情況時間複雜度為：

\[
O(VE)
\]

- `dist` 和 `prev` 各需要為每個頂點保存一筆資訊，輔助空間為：

\[
O(V)
\]

以上空間複雜度不包含圖本身的儲存空間。若使用提前停止，某些輸入可能在少於 \(|V|-1\) 輪時就結束，但最壞情況時間複雜度仍為 \(O(VE)\)。完整演算法比較會放在 [[07-time-complexity|Time Complexity]]。[R5]

## 12. 常見誤解

1. **誤解：只要有負權重邊，就一定有負權重環。**
   - 正確：負權重邊與負權重環是不同概念；沒有負環時，Bellman-Ford 仍可得到有限最短距離。
2. **誤解：圖中任何地方有負環，來源點到所有頂點的距離都會變成 \(-\infty\)。**
   - 正確：負環必須從來源點可達；而只有該環能繼續到達的頂點，才會受到成本無限下降的影響。
3. **誤解：Bellman-Ford 每輪只檢查一條邊。**
   - 正確：每輪會掃描整個邊集合；更新能否在同一輪傳播多條邊，會受到邊的掃描順序影響。
4. **誤解：做完 \(|V|-1\) 輪就能知道是否有負環。**
   - 正確：還需要額外掃描邊，檢查是否仍有可改善的距離。
5. **誤解：Bellman-Ford 使用 Min-Priority Queue 選擇下一個頂點。**
   - 正確：Bellman-Ford 的標準版本反覆掃描所有邊，不使用 Dijkstra 的最小距離頂點選取流程。

## 13. 相關概念

- [[02-graph-weighted-graph|Graph & Weighted Graph]]：理解頂點、邊、有向圖與邊權重。
- [[03-shortest-path-problem|Shortest Path Problem]]：理解單源最短路徑問題。
- [[04-dijkstra-algorithm|Dijkstra Algorithm]]：比較非負權重限制與貪婪選取策略。
- [[05-edge-relaxation|Edge Relaxation]]：理解 Bellman-Ford 反覆使用的距離更新操作。
- [[07-time-complexity|Time Complexity]]：比較 Bellman-Ford 與其他演算法的效率。
- [[08-bfs|BFS]]：比較非加權圖的最短路徑方法。
- [[10-algorithm-comparison|Algorithm Comparison]]：統整 BFS、Dijkstra、Bellman-Ford 的適用情境。

## 14. 參考資料

- **R5a**：MIT OpenCourseWare, 6.006 Introduction to Algorithms, *Lecture 12: Bellman-Ford*, Spring 2020. <https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/2430d7903a5529451d80c17f89a41fe8_MIT6_006S20_lec12.pdf>
- **R5b**：MIT OpenCourseWare, 6.006 Introduction to Algorithms, *Recitation 12: Bellman-Ford*, Spring 2020. <https://live.ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/1a34924a2ee4e08c580c2268f8e854ee_MIT6_006S20_r12.pdf>
- **R2**：NetworkX Developers, *Shortest Paths*. <https://networkx.org/documentation/stable/reference/algorithms/shortest_paths.html>
- **R3**：Cornell University, CS 2110, *23. Shortest Paths*, Fall 2026. <https://courses.cis.cornell.edu/courses/cs2110/2026fa/lectures/lec23/>

**來源使用範圍：** R5a 用於負權重環、最短路徑有限性及進階理論補充；R5b 用於初學者版 Bellman-Ford 流程、`|V|-1` 輪鬆弛、額外負環檢查與複雜度。R2、R3 僅作為演算法選擇及 Dijkstra 比較的交叉參照。
