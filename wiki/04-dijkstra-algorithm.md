---
title: Dijkstra Algorithm
aliases:
  - Dijkstra 演算法
  - 戴克斯特拉演算法
summary: Dijkstra Algorithm 在所有邊權重非負的加權圖中，求單一來源點到所有可達頂點的最短距離，並可利用前驅紀錄還原路徑與建立最短路徑樹。
page_type: algorithm
related_concepts:
  - Graph & Weighted Graph
  - Shortest Path Problem
  - Edge Relaxation
  - Priority Queue
  - Time Complexity
  - BFS
  - Bellman-Ford
  - Algorithm Comparison
references:
  - R1
  - R2
  - R3
  - R6
status: reviewed-and-ingested
---

# Dijkstra Algorithm（Dijkstra 最短路徑演算法）

## 1. 本頁目的

本頁介紹 Dijkstra Algorithm 如何在**所有邊權重皆非負**的加權圖中，找出指定來源頂點到其他可達頂點的最短距離。讀完後，應能說明演算法為什麼每次選擇目前距離最小的頂點、如何更新距離，以及如何還原實際路徑。

開始前，建議先閱讀 [[02-graph-weighted-graph|Graph & Weighted Graph]] 與 [[03-shortest-path-problem|Shortest Path Problem]]。

## 2. Dijkstra 要解決什麼問題？

**Dijkstra Algorithm** 解決 **Single-Source Shortest Paths（單源最短路徑）**問題：給定來源頂點（source）\(s\)，計算從 \(s\) 到每個可達頂點的最短距離。若同時記錄每個頂點在目前最佳路徑中的前一個頂點，也可以還原實際路徑。

它適用於有向圖或無向圖，但每條邊的權重都必須是非負數：

\[
\forall (u,v)\in E,\quad w(u,v)\geq 0
\]

- **允許零權重邊**，例如 \(w(u,v)=0\)。
- **不允許負權重邊**。若問題包含負權重邊，應考慮 [[09-bellman-ford|Bellman-Ford]]，而不是直接使用 Dijkstra。
- 對無向圖而言，一條無向邊可視為兩個方向、權重相同的有向邊；兩個方向都必須符合非負權重條件。

非負權重是演算法正確性的關鍵，不只是程式輸入格式的限制。因為每條邊的額外成本都不會是負數，路徑繼續往後走時，總成本不會因新增邊而下降。[R3]

## 3. 核心資料與頂點狀態

Dijkstra 需要記錄每個頂點目前已知的距離、路徑前驅，以及是否已經確定最短距離。

| 名稱 | 意義 |
|---|---|
| `dist[v]` | 目前找到的來源點 \(s\) 到 \(v\) 的最短距離估計；尚未找到路徑時為 \(\infty\)。 |
| `prev[v]` | 目前最佳路徑中，\(v\) 的前一個頂點；用來還原路徑。 |
| `settled` | 最短距離已經確定的頂點集合。 |
| `PQ` | 依 `dist` 由小到大取出頂點的 Min-Priority Queue（最小優先佇列）。 |

### 3.1 三種頂點狀態

為了理解演算法，可以將頂點分成三種狀態：

| 狀態 | 意思 | 距離是否確定？ |
|---|---|---|
| **Undiscovered**（尚未發現） | 尚未找到從來源點到它的路徑，因此目前沒有有限的距離估計。 | 否 |
| **Frontier**（候選／邊界） | 已找到至少一條路徑，但還沒被取出並確定最短距離。 | 否，仍可能改善 |
| **Settled**（已確定） | 已從最小優先佇列取出，並依 Dijkstra 的不變量確定最短距離。 | 是 |

`dist[v]` 在 Frontier 階段只是目前找到的最佳候選值，不應提前稱為最終答案。只有頂點被選出並 settled 後，才可將該距離視為最短距離。[R3]

## 4. 核心想法：Greedy Strategy 與 Dijkstra Invariant

### 4.1 Greedy Strategy（貪婪策略）

Dijkstra 每一輪都從 Frontier 中，選出目前 `dist` 最小的頂點。這是一種貪婪策略：每一步先選擇當下看起來距離來源點最近、而且尚未確定的頂點。

但為什麼這樣選是安全的？關鍵就在於所有邊權重都不能為負。

### 4.2 Dijkstra Invariant（Dijkstra 不變量）

**不變量的白話說明：** 在所有尚未確定的頂點中，若某個頂點的暫定距離最小，那麼這個距離已經不可能再被其他路徑縮短，因此可以安全地將它標記為 Settled。

例如，Frontier 中目前有：

| 頂點 | 暫定距離 |
|---|---:|
| B | 7 |
| C | 4 |
| D | 9 |

演算法會先選擇 C，因為 4 是最小值。只要邊權重皆非負，未來經過其他尚未確定的頂點再走到 C，不可能產生比 4 更小的總成本。

**為什麼非負權重很重要？** 可以想像任何一條試圖繞路回到 C 的替代路徑。它必須先經過某個 Frontier 頂點；而從該 Frontier 頂點繼續往後走的權重總和不會小於零，所以替代路徑不可能比目前最小的 Frontier 距離更短。這就是每輪可以安全確定一個頂點的原因。[R3]

> **記住：** Dijkstra 不是「第一次看到某個頂點就確定它的距離」。它會持續比較候選路徑，直到該頂點以目前最小距離從 Priority Queue 取出時，才確定最短距離。

## 5. 演算法執行步驟

給定圖 \(G=(V,E)\) 與來源頂點 \(s\)，基本流程如下：

1. **初始化距離：** 將所有 `dist[v]` 設為 \(\infty\)，所有 `prev[v]` 設為 `NIL`；來源點 `dist[s] = 0`。
2. **初始化佇列：** 建立 Min-Priority Queue，先將來源點 \(s\) 以優先值 0 放入。
3. **選擇頂點：** 從 PQ 取出目前 `dist` 最小的 Frontier 頂點 \(u\)，將它加入 `settled`。
4. **檢查外連邊：** 對每條從 \(u\) 出發的邊 \((u,v)\)，計算候選距離：

   \[
   candidate=dist[u]+w(u,v)
   \]

5. **更新距離（Relaxation）：** 若 `candidate < dist[v]`，代表找到更短路徑，更新 `dist[v]` 與 `prev[v]`，並同步更新 PQ 中 \(v\) 的優先值。
6. **重複執行：** 持續選取最小的 Frontier 頂點，直到 PQ 為空。此時所有從來源點可達的頂點都已 settled。

如果只需要來源點到特定目的頂點 \(t\) 的最短路徑，可以在 **\(t\) 被取出並 settled 時**提前停止；不能只因為第一次發現 \(t\) 就停止，因為後續仍可能找到更短的候選路徑。[R3]

詳細的距離更新概念請見 [[05-edge-relaxation|Edge Relaxation]]；PQ 的操作方式請見 [[06-priority-queue|Priority Queue]]。

## 6. 執行範例：從 A 找到所有可達頂點的最短距離

考慮以下加權圖，從 A 出發。為避免方向誤讀，本例的邊全部視為**雙向邊**，兩個方向權重相同。

| 邊 | 權重 |
|---|---:|
| A—B | 4 |
| A—C | 2 |
| C—B | 1 |
| B—D | 5 |
| C—D | 8 |
| C—E | 10 |
| D—E | 2 |
| D—F | 6 |
| E—F | 3 |

### 6.1 逐輪執行

初始化後：`dist[A]=0`，其他頂點距離皆為 \(\infty\)，PQ 中只有 A。

| 輪次 | Settled 頂點 | 本輪檢查與更新 | 更新後的重要暫定距離 |
|---|---|---|---|
| 初始化 | — | `dist[A]=0` | A=0，其餘為 ∞ |
| 1 | A (0) | B=4、C=2 | C=2、B=4 |
| 2 | C (2) | B=min(4, 2+1)=3；D=10；E=12 | B=3、D=10、E=12 |
| 3 | B (3) | D=min(10, 3+5)=8 | D=8、E=12 |
| 4 | D (8) | E=min(12, 8+2)=10；F=14 | E=10、F=14 |
| 5 | E (10) | F=min(14, 10+3)=13 | F=13 |
| 6 | F (13) | 沒有更短的距離需要更新 | 完成 |

每一輪都先選擇 PQ 中距離最小的候選頂點。注意 B 的距離先從 4 改善成 3，D 的距離從 10 改善成 8，F 的距離從 14 改善成 13；這些都是因為演算法後來發現更短的路徑。

### 6.2 最終結果

| 頂點 | 最短距離 | `prev` 前驅頂點 |
|---|---:|---|
| A | 0 | — |
| B | 3 | C |
| C | 2 | A |
| D | 8 | B |
| E | 10 | D |
| F | 13 | E |

因此，從 A 到 F 的最短路徑為：

\[
A\rightarrow C\rightarrow B\rightarrow D\rightarrow E\rightarrow F
\]

總成本為：

\[
2+1+5+2+3=13
\]

## 7. 虛擬碼

以下使用**支援 `decrease_key` 的索引式 Min-Priority Queue**。這種設計會讓每個頂點在 Frontier 中至多有一個佇列項目；距離改善時，直接降低該頂點的優先值。

```text
DIJKSTRA(G, source):
    for each vertex v in G:
        dist[v] = infinity
        prev[v] = NIL

    settled = empty set
    dist[source] = 0

    PQ = indexed min-priority-queue()
    PQ.insert(source, priority = 0)

    while PQ is not empty:
        u = PQ.extract_min()
        settled.add(u)

        for each outgoing edge (u, v) with weight w(u, v):
            if v is not in settled:
                candidate = dist[u] + w(u, v)

                if candidate < dist[v]:
                    dist[v] = candidate
                    prev[v] = u

                    if PQ.contains(v):
                        PQ.decrease_key(v, candidate)
                    else:
                        PQ.insert(v, priority = candidate)

    return dist, prev
```

此虛擬碼假設：

- 所有邊權重非負。
- PQ 支援 `extract_min`、`insert`、`contains` 與 `decrease_key`。
- `settled` 中的頂點不再更新；非負權重與 Dijkstra invariant 保證已 settled 頂點不需要重新打開。
- 若某頂點從來源點不可達，它不會被加入 PQ；最後 `dist[v]` 仍為 \(\infty\)，`prev[v]` 仍為 `NIL`。

### 7.1 另一種常見實作：Lazy Deletion

有些程式使用一般 Min-Heap，但不支援直接修改既有項目的優先值。這時每次找到更短距離，就把新的 `(candidate, v)` 再放入 Heap；取出時若該筆距離已不是目前 `dist[v]`，就略過這筆過期項目。

這種做法稱為 **Lazy Deletion（延遲刪除）**。它與上方的 `decrease_key` 寫法是兩種不同的佇列管理方式，實作與複雜度分析必須一致，不能把兩者的操作假設混在同一份虛擬碼中。

## 8. 如何還原最短路徑？

`dist[v]` 只記錄來源點到 \(v\) 的最短距離；若要知道實際經過哪些頂點，就要利用每次成功更新距離時記錄的 `prev[v]`。

還原方式：

1. 從目的頂點 \(t\) 開始。
2. 讀取 `prev[t]`，移動到前一個頂點。
3. 持續沿著 `prev` 往回追蹤，直到來源頂點 \(s\)。
4. 將回溯得到的順序反轉，即為從來源到目的地的路徑。

本例中：

- `prev[F]=E`
- `prev[E]=D`
- `prev[D]=B`
- `prev[B]=C`
- `prev[C]=A`

反向回溯後得到：A → C → B → D → E → F。

若目的頂點不可達，`dist[t]=∞` 且 `prev[t]=NIL`，就不存在可還原的路徑。

## 9. Shortest-Path Tree（最短路徑樹）與前驅性質

Dijkstra 不只可以計算來源點到各頂點的最短距離，也能利用 `prev` 記錄，為每個可達頂點選出一個前驅，進而形成一棵 **Shortest-Path Tree（最短路徑樹）**。[R6]

### 9.1 什麼是最短路徑樹？

給定來源點 `s`，對每個從 `s` 可達、且不是 `s` 本身的頂點 `v`，保留一條由 `prev[v]` 指向 `v` 的前驅邊。這些選定的邊會形成一棵以 `s` 為根的樹；在有向圖中，可將它視為由根向外延伸的 rooted arborescence。若原圖是無向圖，則可將選出的樹邊視為無向樹邊，箭頭只用來表示「前驅到子頂點」的關係。[R6]

這棵樹具有以下特性：

- 根節點是來源點 `s`。
- 每個可達且非來源點的頂點，在這棵樹中恰有一個選定的前驅。
- 從根沿著樹邊走到任一可達頂點，得到的路徑都是一條最短路徑。
- 不可達頂點不會出現在這棵以 `s` 為根的最短路徑樹中。

若存在多條同樣短的路徑，`prev` 可能依照頂點處理順序與更新規則選出不同前驅。因此，最短路徑樹通常**不一定唯一**；它代表演算法選定的一組最短路徑，而不是把圖中所有可能的最短路徑全部合併。[R6]

### 9.2 用 A–F 範例建立最短路徑樹

沿用本頁前面的範例，Dijkstra 完成後的前驅紀錄為：

| 頂點 | `prev` 前驅 | 選定的樹邊 | 邊權重 |
|---|---|---|---:|
| A | — | —（根節點） | — |
| C | A | A → C | 2 |
| B | C | C → B | 1 |
| D | B | B → D | 5 |
| E | D | D → E | 2 |
| F | E | E → F | 3 |

因此，選出的樹邊為 A → C、C → B、B → D、D → E、E → F。這個例子的最短路徑樹剛好呈現一條鏈；若其他頂點的前驅不同，樹也可能形成多個分支。由 A 沿樹邊到 F 的總權重為 `2 + 1 + 5 + 2 + 3 = 13`，與前面計算的最短距離一致。

### 9.3 前驅性質與最佳子結構

**前驅性質（Predecessor Property）**可以這樣理解：若一條從來源點 `s` 到 `v` 的最短路徑，最後一條邊是 `(u, v)`，那麼這條路徑從 `s` 到 `u` 的前綴，也必須是 `s` 到 `u` 的最短路徑。[R6]

原因是：假設 `s` 到 `u` 的前綴不是最短路徑，就代表存在另一條更短的 `s` 到 `u` 路徑。把原本較長的前綴換成這條更短路徑，再接上相同的 `(u, v)` 邊，就會得到一條更短的 `s` 到 `v` 路徑，這與原路徑是最短路徑的假設矛盾。

這是最短路徑問題的 **Optimal Substructure（最佳子結構）**：整條最短路徑的前綴，也必須是相應子問題的最短路徑。`prev[v]` 正是把這種前驅關係記錄下來，讓我們可以從目的頂點一路回溯到來源點。

> **注意：** 最短路徑的前綴性質是路徑成本可加總時的最短路徑結構性質，不是 Dijkstra 可以處理負權重邊的理由。Dijkstra 的非負權重限制仍由前面的 Dijkstra Invariant 保證。[R3][R6]

## 10. 適用限制與常見誤解

### 10.1 為什麼不能有負權重邊？

Dijkstra 每次取出目前暫定距離最小的頂點，並將其距離視為已確定。這個決定依賴「路徑再往後增加邊，不會讓總成本下降」。若允許負權重邊，之後可能經過一條負權重邊，反而讓已 settled 頂點出現更小的路徑成本，破壞 Dijkstra invariant。

例如，若 A 到 B 的直接邊成本為 5，而 A 到 C 的成本為 10，C 到 B 的邊成本為 -8，則先取出 B 時會把距離 5 當成確定值，但之後 A → C → B 的成本其實是 2。這說明負權重會破壞演算法的核心保證。

需要處理負權重邊時，請閱讀 [[09-bellman-ford|Bellman-Ford]]。

### 10.2 常見誤解

- **誤解：第一次發現頂點就能確定最短距離。**
  - 正確：第一次發現只得到一個候選距離；之後可能被更短路徑改善，直到 settled 才確定。
- **誤解：Dijkstra 每次選擇距離來源最近的鄰點。**
  - 正確：它是從所有 Frontier 頂點中選距離最小者，不只比較上一個頂點的直接鄰點。
- **誤解：權重必須大於 0。**
  - 正確：允許權重等於 0；限制是權重不得小於 0。
- **誤解：發現目的頂點就可以停止。**
  - 正確：若只求單一目的地，必須等目的頂點被取出並 settled 才能安全停止。
- **誤解：最短距離表本身就包含完整路徑。**
  - 正確：還需要 `prev` 或其他前驅資訊來還原路徑。

## 11. 時間與空間複雜度

複雜度取決於圖的表示方式及 Priority Queue 的實作。常見設定如下：

| 實作方式 | 典型時間複雜度 | 說明 |
|---|---:|---|
| 鄰接串列 + Binary Min-Heap（支援 decrease-key） | \(O((V+E)\log V)\) | 每個頂點取出一次，並對邊進行鬆弛；連通圖常簡寫為 \(O(E\log V)\)。 |
| 鄰接矩陣 + 線性掃描尋找最小暫定距離 | \(O(V^2)\) | 每輪線性尋找下一個頂點，適合較稠密或規模較小的圖。 |
| 鄰接串列 + Fibonacci Heap | \(O(E+V\log V)\) | 利用較低的攤銷優先值更新成本；實作較複雜。 |

使用鄰接串列與 Binary Heap 時，輔助空間通常為 \(O(V)\)：`dist`、`prev`、`settled` 與 PQ 都至多記錄與頂點數量同階的資訊。這裡不包含圖本身的儲存空間。

R3 課程教材使用 heap-backed dynamic priority queue 與 HashMap，分析得到 expected \(O(E\log V)\)；本 Wiki 以較一般的 \(O((V+E)\log V)\) 表示，並在 [[07-time-complexity|Time Complexity]] 頁面進一步說明連通性、可達子圖及資料結構假設。[R3]

## 12. 相關概念

- [[02-graph-weighted-graph|Graph & Weighted Graph]]：理解頂點、邊、權重與圖的表示方式。
- [[03-shortest-path-problem|Shortest Path Problem]]：理解單源最短路徑問題。
- [[05-edge-relaxation|Edge Relaxation]]：深入了解如何比較候選距離並更新 `dist` 與 `prev`。
- [[06-priority-queue|Priority Queue]]：了解 Min-Priority Queue、Heap 與 `decrease_key`。
- [[07-time-complexity|Time Complexity]]：深入分析不同圖表示法與 PQ 實作的效率。
- [[08-bfs|BFS]]：比較無權重圖與加權圖的最短路徑方法。
- [[09-bellman-ford|Bellman-Ford]]：了解如何處理負權重邊。
- [[10-algorithm-comparison|Algorithm Comparison]]：統整三種演算法的適用條件與差異。

## 13. 本頁重點整理

1. Dijkstra 解決非負權重圖的單源最短路徑問題。
2. `dist` 保存目前最佳距離估計，`prev` 保存路徑前驅。
3. 頂點會經歷 Undiscovered、Frontier、Settled 等狀態。
4. 每輪從 Frontier 選出 `dist` 最小的頂點；依 Dijkstra invariant，該頂點的距離可安全確定。
5. 找到更短路徑時，透過 Edge Relaxation 更新距離、前驅與 PQ 優先值。
6. 若只找特定目的地，必須等目的地 settled 才能提前停止。
7. Dijkstra 不適用於含負權重邊的問題；可考慮 Bellman-Ford。
8. 複雜度取決於圖的表示法與優先佇列實作。

## 14. 參考資料

- **R1**：Cornell University, CS 2110, *21. Graphs*（2026 Fall）。用於 Graph、Vertex、Edge、Weighted Graph 等前置概念。<https://www.cs.cornell.edu/courses/cs2110/2026fa/lectures/lec21/>
- **R2**：NetworkX Developers, *Shortest Paths*, NetworkX 3.7 documentation。用於單源最短路徑問題定位與演算法選擇概觀。<https://networkx.org/documentation/stable/reference/algorithms/shortest_paths.html>
- **R3**：Cornell University, CS 2110, *23. Shortest Paths*（2026 Fall）。用於 Dijkstra 的非負權重條件、Frontier／Settled 狀態、不變量、距離與前驅紀錄、動態優先佇列、執行追蹤及複雜度分析。<https://courses.cis.cornell.edu/courses/cs2110/2026fa/lectures/lec23/>
- **R6**：Cornell University, CS 312, *Lecture 26: Finding Shortest Paths*（Spring 2007）。用於補充 Shortest-Path Tree、前驅性質與最短路徑的最佳子結構；不取代 R3 對 Dijkstra 核心流程與正確性的說明。<https://www.cs.cornell.edu/courses/cs312/2007sp/lectures/lec26.html>
