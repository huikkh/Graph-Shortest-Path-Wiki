---
title: Graph & Weighted Graph
aliases:
  - 圖與加權圖
  - Graph
  - Weighted Graph
summary: Graph 以頂點和邊表示物件之間的連結；Weighted Graph 則在邊上附加數值，用來表示成本、時間或距離等資訊。
page_type: concept
related_concepts:
  - Shortest Path Problem
  - Dijkstra Algorithm
  - BFS
  - Bellman-Ford
  - Time Complexity
  - Algorithm Comparison
references:
  - R1
status: reviewed-and-ingested
---

# Graph & Weighted Graph（圖與加權圖）

## 1. 本頁目的

本頁介紹理解最短路徑演算法前需要的圖論基礎，包括頂點、邊、有向圖、無向圖、權重、路徑，以及常見的圖表示方式。讀完後，應能看懂「從一個頂點沿著邊前往另一個頂點」代表什麼，以及路徑成本如何計算。

## 2. Graph 是什麼？

**Graph（圖）**是一種用來表示物件之間連結關係的數學結構，通常表示為：

\[
G=(V,E)
\]

- **Vertex（頂點／節點）**：代表圖中的一個物件，通常以 \(V\) 表示頂點集合。
- **Edge（邊）**：代表兩個頂點之間的連結，通常以 \(E\) 表示邊集合。

例如，若以城市作為頂點、城市之間的道路作為邊，就能用 Graph 表示交通網路。社群網路也可以用使用者作為頂點、使用者之間的關係作為邊。

## 3. 有向圖與無向圖

### 3.1 Directed Graph（有向圖）

有向圖的邊具有方向。邊 \((u,v)\) 表示可以從頂點 \(u\) 沿著這條邊前往頂點 \(v\)，但不會自動代表存在反方向的邊 \((v,u)\)。

- \(u\)：這條有向邊的起點（tail）。
- \(v\)：這條有向邊的終點（head）。

例子：單向道或只能由 A 航往 B 的航班，可以用有向邊 \(A\rightarrow B\) 表示。

### 3.2 Undirected Graph（無向圖）

無向圖的邊沒有方向。若 A 與 B 之間有一條無向邊，表示兩個方向都可以沿著這條連結移動。

例子：若社交關係被視為互相成立，A 與 B 的朋友關係可以用無向邊表示。

> **注意：** 有向圖中的 \(A\rightarrow B\) 不代表 \(B\rightarrow A\) 也存在；無向圖的連結則不區分方向。

## 4. Weighted Graph（加權圖）

**Weighted Graph（加權圖）**是在頂點或邊上附加數值資訊的圖。在最短路徑問題中，通常特別關注邊上的權重（weight）。

邊的權重可以代表：

- 兩個地點之間的距離。
- 從一個地點到另一個地點所需的時間。
- 使用某條連線所需付出的成本。

例如，若 A 到 B 的邊權重是 4，表示沿著這條邊移動的成本為 4。權重的實際意義取決於題目如何定義。

### 4.1 路徑成本

若路徑 \(P\) 經過多條邊，路徑成本通常是沿途邊權重的總和：

\[
Cost(P)=\sum_{e\in P}w(e)
\]

例如，路徑 \(A\rightarrow B\rightarrow C\) 的兩條邊權重分別為 4 與 2，則路徑成本為：

\[
Cost(P)=4+2=6
\]

這個概念會在 [[03-shortest-path-problem|Shortest Path Problem]] 中使用。

### 4.2 權重不一定必須是非負數

Weighted Graph 的一般定義並不要求所有邊權重都必須非負；權重是否允許負數，要看所使用的演算法及問題設定。

**Dijkstra Algorithm 的限制是所有邊權重必須非負**，這是 Dijkstra 的適用條件，不是所有加權圖的定義。若圖中存在負權重邊，應研究其他方法，例如 [[09-bellman-ford|Bellman-Ford]]。

> **來源範圍提醒：** Cornell 教材中的 `WeightedEdge` Java 範例，將該程式類別的權重限制為非負整數。這是該範例實作的限制，不應擴大解讀成所有 Weighted Graph 都不能有負權重。

## 5. Neighbor、Path 與 Cycle

### 5.1 Neighbor（相鄰頂點）

若從頂點 \(u\) 有一條邊可以直接到達頂點 \(v\)，則 \(v\) 是 \(u\) 的鄰點（neighbor）。在有向圖中，需遵守邊的方向。

### 5.2 Path（路徑）

路徑表示沿著圖中的邊，從來源頂點走到目的頂點的一連串連接。每一條邊的終點必須接上下一條邊的起點。

- **Source（來源頂點）**：路徑開始的頂點。
- **Destination（目的頂點）**：路徑結束的頂點。
- **Unweighted Path Length（無權重路徑長度）**：通常以經過的邊數計算。
- **Weighted Path Cost（加權路徑成本）**：通常以經過的邊權重總和計算。

不同教材對 `path`、`walk` 與 `simple path` 的用詞可能略有差異。本 Wiki 在介紹時會明確指出是否允許重複經過頂點；**Simple Path（簡單路徑）**是不重複拜訪頂點的路徑。

### 5.3 Cycle（環）

Cycle 是起點與終點相同、沿著邊繞回原頂點的路徑。環的存在與否，會影響某些圖論問題的分析。

## 6. Graph 的常見表示方式

程式需要將 Graph 儲存在記憶體中。常見方法包括 Adjacency Matrix 與 Adjacency List。

以下使用同一個有向加權圖作為例子：

- 頂點：\(V=\{A,B,C\}\)
- 邊：\(A\rightarrow B\)，權重 4；\(A\rightarrow C\)，權重 7；\(B\rightarrow C\)，權重 2。

### 6.1 Adjacency Matrix（鄰接矩陣）

使用二維矩陣表示頂點之間是否有邊；加權圖也可以在對應格子中存放邊的權重。下表的「—」表示沒有該方向的邊。

| 起點／終點 | A | B | C |
|---|---:|---:|---:|
| A | — | 4 | 7 |
| B | — | — | 2 |
| C | — | — | — |

**特性：**

- 查詢指定兩個頂點之間是否有邊，可透過矩陣位置直接查詢，時間為 \(O(1)\)。
- 儲存空間通常為 \(O(V^2)\)，即使圖很稀疏，也要保留整個矩陣。
- 列出某個頂點的所有鄰點，通常需要掃描該列，時間為 \(O(V)\)。

### 6.2 Adjacency List（鄰接串列）

為每個頂點保存它能直接連到哪些頂點；在加權圖中，也一併保存對應邊的權重。

本例可寫成：

```text
A: (B, 4), (C, 7)
B: (C, 2)
C: 無
```

**特性：**

- 只保存實際存在的邊，空間通常為 \(O(V+E)\)。
- 列出頂點 \(u\) 的鄰點，需走訪該頂點的鄰接項目，時間為 \(O(degree(u))\)。
- 若以一般串列保存鄰點，查詢特定邊可能需要逐一搜尋；若使用雜湊表保存鄰點，平均查詢時間可達 \(O(1)\)。因此，鄰接串列的操作效率也取決於實際採用的底層資料結構。

### 6.3 兩種表示法如何選擇？

| 比較項目 | Adjacency Matrix | Adjacency List |
|---|---|---|
| 儲存空間 | \(O(V^2)\) | 通常 \(O(V+E)\) |
| 查詢指定邊 | \(O(1)\) | 取決於鄰接項目的底層結構；一般串列可能需搜尋 |
| 列出某頂點鄰點 | \(O(V)\) | \(O(degree(u))\) |
| 常見適用情況 | 頂點數較少或圖較密集 | 圖較稀疏、常需走訪鄰點 |

## 7. 與最短路徑演算法的關係

理解 Graph 與 Weighted Graph 後，就能進一步理解最短路徑問題：在圖中指定起點與目的地，找出一條符合邊方向、且總成本最低的路徑。

接下來可閱讀：

- [[03-shortest-path-problem|Shortest Path Problem]]：理解最短路徑的問題定義。
- [[04-dijkstra-algorithm|Dijkstra Algorithm]]：學習在非負權重圖中求單源最短路徑。
- [[08-bfs|BFS]]：理解無權重圖或等權重邊的最短路徑方法。
- [[09-bellman-ford|Bellman-Ford]]：理解如何處理負權重邊。

## 8. 本頁重點整理

1. Graph 由頂點（Vertex）與邊（Edge）組成，用來表示物件之間的連結。
2. 有向圖的邊有方向；無向圖的邊不區分方向。
3. Weighted Graph 在邊上附加權重，可代表距離、時間或成本。
4. 加權路徑的成本通常是沿途邊權重的總和。
5. Weighted Graph 不代表權重一定非負；非負權重是 Dijkstra 的適用條件。
6. 鄰接矩陣以二維表格表示連結；鄰接串列為每個頂點保存鄰點資訊。

## 9. 參考資料

- **R1**：Cornell University, CS 2110, *21. Graphs*（2026 Fall）。涵蓋 Graph 定義、有向圖與無向圖、鄰點、路徑、環、邊權重、Adjacency Matrix 與 Adjacency List。
  - <https://www.cs.cornell.edu/courses/cs2110/2026fa/lectures/lec21/>
  - 本頁主要依據教材中 Defining Graphs、Other Graph Varieties、Graph Substructures、Labeled Graphs、Graph Representations 等段落整理。
