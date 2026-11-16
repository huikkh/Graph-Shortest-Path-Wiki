---
source_id: R6
institution: Cornell University
course: CS 312
source_title: Lecture 26 — Finding Shortest Paths
term: Spring 2007
source_type: university_course_lecture_notes
url: https://www.cs.cornell.edu/courses/cs312/2007sp/lectures/lec26.html
planned_use:
  - Dijkstra Algorithm
review_status: approved
ingest_status: ingested
---

# R6 Review — Cornell CS 312: Lecture 26, Finding Shortest Paths

## 1. 來源摘要

- **機構：** Cornell University
- **課程：** CS 312
- **教材：** Lecture 26 — Finding Shortest Paths
- **學期：** Spring 2007（歷年課程教材）
- **原始網址：** <https://www.cs.cornell.edu/courses/cs312/2007sp/lectures/lec26.html>
- **來源定位：** 本資料是 Dijkstra 主頁的補充來源，不取代 R3（Cornell CS 2110, 2026 Fall）。

教材說明加權有向圖上的最短路徑問題，並介紹 Dijkstra 的 frontier／completed 概念、逐輪距離更新、路徑重建、最短路徑樹、正確性不變量與複雜度。

## 2. 與既有 Wiki 的比對

目前 `wiki/04-dijkstra-algorithm.md` 已由 R3 建立，包含：

- Dijkstra 的單源最短路徑目標與非負權重條件。
- Undiscovered、Frontier、Settled 三種狀態。
- `dist`、`prev`、Greedy Strategy 與 Dijkstra Invariant。
- Min-Priority Queue、距離改善、`decrease_key` 與 Lazy Deletion 的差別。
- 提前停止、A–F 執行範例、虛擬碼、路徑還原與複雜度。

因此 R6 不應重新建立第二個 Dijkstra 頁面，也不應重複匯入上述基礎內容。

## 3. R6 的補充價值

### 3.1 Shortest-Path Tree（最短路徑樹）

對每個從來源點可達、且不是來源點本身的頂點，選擇一個位於其最短路徑上的前驅頂點，並保留對應的前驅邊。這些選定的前驅邊可以組成一棵以來源點為根的最短路徑樹（在有向圖中可視為 rooted arborescence）。若存在多條同長最短路徑，前驅選擇可能不唯一；Wiki 應說明「選定一組前驅邊可形成一棵樹」，而不是宣稱所有最短路徑邊合在一起必然只有一棵樹。

### 3.2 Predecessor / Optimal-Substructure Property

若 `prev[v] = u` 是所選最短路徑上的前驅，則所選的來源點到 `v` 的最短路徑，等於來源點到 `u` 的一條最短路徑，再接上邊 `(u, v)`。原因是：若前段到 `u` 不是最短路徑，就可以用更短的來源點到 `u` 路徑替換，再接上同一條 `(u, v)`，得到更短的到 `v` 路徑，與原路徑最短矛盾。

這個性質可補足 `prev` 不只是程式中的回溯指標，也有最短路徑結構上的意義。

### 3.3 樹狀結構與路徑還原的關係

可沿用既有 A–F 範例說明：`prev[C]=A`、`prev[B]=C`、`prev[D]=B`、`prev[E]=D`、`prev[F]=E`。這組前驅關係形成以 A 為根的樹（本例剛好呈現鏈狀），從目的頂點沿 `prev` 回溯即可還原來源到目的地的路徑。

## 4. 不建議重複匯入的內容

| R6 教材內容 | 現有頁面狀況 | Review 處理 |
|---|---|---|
| Dijkstra 是 Greedy Algorithm | R3 已說明 | 不重複 |
| Frontier / Completed 狀態 | R3 已整理為 Undiscovered / Frontier / Settled | 不重複 |
| 非負權重的重要性與正確性不變量 | R3 已有 | 不重複；R3 保持主要依據 |
| Min-Priority Queue 與更新操作 | R3、R4 已有 | 不重複 |
| 目的頂點 settled 後提前停止 | R3 已有 | 不重複 |
| Binary Heap 與 Fibonacci Heap 複雜度 | R3、R4，並預留第 07 頁 | 不在本次補充 |
| Shortest-Path Tree 與 predecessor property | R3 尚未明確獨立說明 | 建議新增短節 |

## 5. 建議 Ingest 範圍

**目標頁面：** 僅更新既有 `wiki/04-dijkstra-algorithm.md`，不建立新頁面。

建議新增一個短節：`Shortest-Path Tree（最短路徑樹）與前驅性質`，內容包含：

1. 什麼是最短路徑樹。
2. 如何從 `prev[v]` 選出樹邊。
3. 為什麼最短路徑的前綴也必須是最短路徑（optimal substructure）。
4. 用既有 A–F 範例解讀前驅邊與樹狀結構。
5. 說明若存在多條等長最短路徑，樹的選擇可能不唯一。

在該節及頁面 references 中標註 R6。現有演算法步驟、虛擬碼、範例數值及複雜度不需因 R6 而重寫。

## 6. 來源限制與注意事項

- R6 是 2007 年的 Cornell 課程教材，適合作為 Dijkstra 結構性質的補充，不應凌駕較新的 R3 課程資料。
- 教材頁面另有路徑應用、All-pairs／Floyd–Warshall 與複雜度討論；這些內容已由 R2、R3 或未來的第 07、10 頁承擔，本次不重複擷取。
- 教材部分複雜度推導使用連通圖假設，並以 `V = O(E)` 化簡；若未明示此前提，不應將簡化式直接當成所有圖的通用複雜度。
- R3 已明確提供非負權重條件；R6 的補充內容不得讓讀者誤以為 Dijkstra 可處理負權重邊。
- 對零權重邊、相同距離或多條等長最短路徑的情況，不應宣稱最短路徑樹的前驅選擇唯一。

## 7. Review 結論

**建議：通過 Review，但採取窄範圍補充。**

R6 具可信的 Cornell 課程來源，對初學者理解 `prev`、最短路徑樹及路徑前綴性質有幫助。然而它與 R3 在演算法原理、Greedy、Frontier、優先佇列、提前停止及複雜度方面重複度高，因此只建議補充 Shortest-Path Tree 與 predecessor / optimal-substructure property。

- Review status：Approved by user on 2026-11-16
- Ingest status：Ingested into the existing Dijkstra Algorithm page on 2026-11-16
- The existing `wiki/04-dijkstra-algorithm.md` was updated with the approved Shortest-Path Tree and predecessor-property section; no new Dijkstra page was created.


## Ingest 結果

使用者已核准。R6 的 Shortest-Path Tree 與 predecessor／optimal-substructure property 已整合至既有 `wiki/04-dijkstra-algorithm.md`。重複的 Greedy、Frontier／Settled、非負權重條件、Priority Queue 與複雜度內容維持由 R3／R4 支援。
