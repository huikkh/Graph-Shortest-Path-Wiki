---
title: Shortest Path Problem
aliases:
  - 最短路徑問題
  - Shortest Paths
summary: 最短路徑問題是在圖中尋找一條符合邊方向、且路徑成本最低的路徑；成本可由邊數或邊權重總和定義。
page_type: concept
related_concepts:
  - Graph & Weighted Graph
  - Dijkstra Algorithm
  - BFS
  - Bellman-Ford
  - Algorithm Comparison
  - Time Complexity
references:
  - R1
  - R2
status: reviewed-and-ingested
---

# Shortest Path Problem（最短路徑問題）

## 1. 本頁目的

本頁介紹最短路徑問題的意思，以及如何依照圖的權重設定與查詢需求，選擇合適的演算法。讀完後，應能分辨「邊數最少」與「總權重最低」的差異，也能理解 single-pair、single-source、all-pairs 三種常見查詢範圍。

開始前，建議先閱讀 [[02-graph-weighted-graph|Graph & Weighted Graph]]，了解頂點、邊、權重與路徑成本。

## 2. 什麼是最短路徑？

**Shortest Path（最短路徑）**是在圖中，從指定起點走到指定終點的所有可行路徑裡，找出「路徑成本」最小的一條。

這裡的「最短」不一定代表地圖上的幾何距離，也不一定代表經過最少的邊；它取決於題目如何定義每條邊的成本。

若一條路徑為：

\[
P=(v_0,v_1,\ldots,v_k)
\]

而每條邊 \((v_{i-1},v_i)\) 的權重為 \(w(v_{i-1},v_i)\)，則加權路徑成本可寫成：

\[
Cost(P)=\sum_{i=1}^{k}w(v_{i-1},v_i)
\]

最短路徑就是在所有符合圖的連線與方向限制的路徑中，使這個總成本最小的路徑。[R1][R2]

## 3. 非加權圖與加權圖的差異

### 3.1 Unweighted Graph（非加權圖）

非加權圖沒有為邊指定不同的成本。計算最短路徑時，通常將每條邊視為成本相同（例如每條邊成本都是 1），因此最短路徑就是**經過邊數最少**的路徑。

這種情況常使用 **Breadth-First Search（BFS，廣度優先搜尋）**尋找最少邊數的路徑。詳見 [[08-bfs|BFS]]。

### 3.2 Weighted Graph（加權圖）

加權圖的邊具有權重，例如距離、通行時間或費用。最短路徑需要比較路徑上所有邊的權重總和，而不是只比較經過幾條邊。

例如：

- 路徑 A → B → D → F 經過 3 條邊，總權重為 \(4+10+11=25\)。
- 路徑 A → C → E → D → F 經過 4 條邊，總權重為 \(2+3+4+11=20\)。

第二條路徑雖然經過比較多條邊，但總權重較小，因此它才是這個加權圖中的最短路徑。[R2]

> **重要觀念：** 最少邊數不一定等於最低總成本。只有在每條邊成本相同且非負時，兩種判斷方式才會一致。

## 4. 有向圖與無向圖的路徑限制

最短路徑問題可以出現在有向圖或無向圖中：

- **無向圖（Undirected Graph）**：邊沒有方向，可以沿著連線的任一方向移動。
- **有向圖（Directed Graph）**：邊具有方向，路徑必須依照箭頭方向前進；存在 \(u\rightarrow v\) 不代表也存在 \(v\rightarrow u\)。

這是路徑是否可行的限制；路徑成本則另外依照邊數或權重總和計算。圖的基本定義見 [[02-graph-weighted-graph|Graph & Weighted Graph]]。[R1][R2]

## 5. 三種常見的查詢範圍

最短路徑問題除了依照權重分類，也可以依照想知道的起點與終點範圍分類。[R2]

| 查詢類型 | 問題 | 例子 |
|---|---|---|
| **Single-pair**（單一起終點） | 指定一個起點與一個終點，尋找兩者之間的最短路徑 | 從 A 到 F 的最短路徑 |
| **Single-source**（單一起點） | 指定一個起點，尋找它到所有可達頂點的最短路徑 | 從 A 到其他所有可達頂點 |
| **All-pairs**（所有頂點對） | 尋找圖中每一組頂點之間的最短路徑 | 計算所有城市彼此之間的最短路徑 |

Dijkstra、BFS 與 Bellman-Ford 都常用於 single-source 或 single-pair 問題；all-pairs 則通常需要考慮專門的全點對演算法，例如 Floyd–Warshall 或 Johnson。這份 Wiki 的主要教學範圍先聚焦前三種演算法，不另外建立全點對演算法專頁。[R2]

## 6. 如何初步選擇最短路徑演算法？

演算法選擇通常要先看邊權重的條件，再看查詢範圍。以下是本 Wiki 會深入介紹的三種演算法概觀：[R2]

| 演算法 | 常見適用情況 | 核心想法 | 典型時間複雜度 |
|---|---|---|---:|
| **BFS** | 非加權圖（或每條邊成本相同且非負） | 由近到遠逐層探索，先找到邊數最少的路徑 | \(O(V+E)\) |
| **Dijkstra** | 邊權重皆非負的加權圖 | 每次確定目前暫定距離最小的頂點，再更新鄰點距離 | \(O((V+E)\log V)\) |
| **Bellman-Ford** | 可能包含負權重邊的加權圖 | 重複檢查並鬆弛各條邊 | \(O(VE)\) |

其中：

- \(V\)：頂點（Vertex）的數量。
- \(E\)：邊（Edge）的數量。

表中的複雜度是常見實作下的概觀，不是脫離實作條件後仍適用的固定數值。尤其 Dijkstra 的複雜度會受到圖的表示方式與優先佇列實作影響；詳細前提會在 [[07-time-complexity|Time Complexity]] 說明。

### 6.1 選擇時要注意

1. **沒有不同的邊權重**：通常可用 BFS 找到邊數最少的路徑。
2. **有權重且全部非負**：可使用 Dijkstra；它不適合直接處理負權重邊。
3. **可能存在負權重邊**：可考慮 Bellman-Ford，並且還要檢查是否存在從來源點可到達、且會影響目標路徑的負權重環。若存在相關負環，最短距離可能沒有有限的最小值。
4. **需要所有頂點對的結果**：應依問題規模與圖的特性考慮 all-pairs 演算法，而不是只把 single-source 方法當成唯一選擇。

本節是演算法選擇的初步指引；各演算法的正確性、步驟與限制，請閱讀對應頁面：[[04-dijkstra-algorithm|Dijkstra Algorithm]]、[[08-bfs|BFS]]、[[09-bellman-ford|Bellman-Ford]]。

## 7. 生活化例子：選擇交通路線

假設你要從家裡前往學校：

- 如果每條道路只代表「走過一條路」，而且所有道路成本相同，你可以比較需要經過幾條道路。
- 如果每條道路標示不同的距離、時間或費用，就要把沿途成本加總，再比較哪條路線的總成本最低。

因此，最短路徑問題的第一個問題不是「哪條路看起來比較短」，而是**題目要最小化的成本究竟是什麼**。

## 8. 本頁重點整理

1. 最短路徑是在所有可行路徑中，找出總成本最低的路徑。
2. 非加權圖通常以經過的邊數計算路徑長度；加權圖則以邊權重總和計算成本。
3. 有向圖必須遵守邊的方向；無向圖的邊可雙向通行。
4. Single-pair、Single-source、All-pairs 分別代表單一起終點、單一起點到所有可達頂點，以及所有頂點對的查詢。
5. BFS 常用於非加權圖；Dijkstra 適用於非負權重；Bellman-Ford 可處理負權重邊，但仍須留意負權重環。
6. 複雜度比較必須同時交代演算法實作與資料結構的前提。

## 9. 相關概念

- [[02-graph-weighted-graph|Graph & Weighted Graph]]：理解圖、邊、權重與路徑成本。
- [[04-dijkstra-algorithm|Dijkstra Algorithm]]：學習非負權重圖的單源最短路徑。
- [[08-bfs|BFS]]：學習非加權圖的最短路徑。
- [[09-bellman-ford|Bellman-Ford]]：學習如何處理負權重邊。
- [[10-algorithm-comparison|Algorithm Comparison]]：比較三種演算法的適用條件與差異。
- [[07-time-complexity|Time Complexity]]：深入了解不同實作的時間與空間成本。

## 10. 參考資料

- **R1**：Cornell University, CS 2110, *21. Graphs*（2026 Fall）。用於 Graph、Weighted Graph、Path、Edge Weight 等前置概念。<https://www.cs.cornell.edu/courses/cs2110/2026fa/lectures/lec21/>
- **R2**：NetworkX Developers, *Shortest Paths*, NetworkX 3.7 documentation。用於最短路徑定義、加權與非加權圖、查詢類型及演算法選擇。<https://networkx.org/documentation/stable/reference/algorithms/shortest_paths.html>

**來源使用範圍：** 本頁使用 NetworkX 文件中的問題定義、演算法摘要表及查詢類型說明；NetworkX API 的參數細節沒有當成一般圖論定義匯入。[R2]
