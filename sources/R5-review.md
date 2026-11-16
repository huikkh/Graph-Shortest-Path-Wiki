---
source_id: R5
institution: Massachusetts Institute of Technology
course: 6.006 Introduction to Algorithms
term: Spring 2020
source_title:
  - Lecture 12: Bellman-Ford
  - Recitation 12: Bellman-Ford
source_type: university_lecture_notes_and_recitation
urls:
  lecture: https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/2430d7903a5529451d80c17f89a41fe8_MIT6_006S20_lec12.pdf
  recitation: https://live.ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/1a34924a2ee4e08c580c2268f8e854ee_MIT6_006S20_r12.pdf
proposed_used_by:
  - Bellman-Ford
  - Edge Relaxation
  - Algorithm Comparison
review_status: approved
ingest_status: ingested
---

# R5 Review — MIT 6.006: Bellman-Ford

## 1. 來源選擇與組成

本次將 MIT OpenCourseWare 的兩份同一課程教材視為一組 R5 來源：

1. **Lecture 12 — Bellman-Ford**：介紹負權重圖、負權重環、簡單最短路徑、Bellman-Ford 的分層圖（graph duplication）觀點、正確性與複雜度。
2. **Recitation 12 — Bellman-Ford**：提供較適合初學者的標準 Bellman-Ford 版本：初始化距離、重複 `|V|-1` 輪鬆弛所有邊，再額外掃描一次以檢查可從來源點到達的負權重環；並附 Python 程式碼、正確性說明與 `O(VE)` 時間複雜度。

Lecture 12 原始來源：
<https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/2430d7903a5529451d80c17f89a41fe8_MIT6_006S20_lec12.pdf>

Recitation 12 原始來源：
<https://live.ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/1a34924a2ee4e08c580c2268f8e854ee_MIT6_006S20_r12.pdf>

## 2. 建議擷取範圍

### 納入 Bellman-Ford 頁面（09）

- **問題定位**：Bellman-Ford 解決單源最短路徑；與 Dijkstra 相比，可處理負權重邊。
- **初始化**：所有頂點距離設為 `∞`，來源點距離設為 `0`；前驅紀錄先設為空。
- **Relaxation**：重複掃描所有邊，若 `dist[u] + w(u,v) < dist[v]`，便更新 `dist[v]` 與 `prev[v]`。
- **為什麼是 `|V|-1` 輪**：若不存在相關負權重環，有限最短路徑可取為 simple path；simple path 不重複頂點，最多包含 `|V|-1` 條邊。每輪掃描所有邊後，會逐步涵蓋邊數更多的最短路徑。
- **負權重環檢查**：完成 `|V|-1` 輪後，再檢查是否仍有可鬆弛的邊。若存在一條從來源點可達、且仍可改善距離的邊，代表存在從來源點可達的負權重環。
- **提早停止**：若某一輪沒有任何距離更新，可提前停止，因為後續輪次也不會再改善距離。
- **複雜度**：一般實作時間 `O(VE)`；儲存 `dist` 與 `prev` 的輔助空間為 `O(V)`（不計圖本身）。
- **路徑還原**：透過 `prev` 前驅指標回溯，概念與 Dijkstra 類似。

### 進階補充：負權重環的影響

若一個負權重環可從來源點到達，且某個頂點也能從該環到達，則來源點到該頂點的路徑可以反覆繞行該環，讓總成本無限下降；因此該頂點的最短距離不是有限數值，而可視為 `−∞`。不能簡化成「圖中只要任何地方有負環，所有頂點距離都變成 −∞」；負環必須從來源點可達，且要看哪些頂點能從該環繼續到達。

標準 Recitation 版本主要回報是否存在從來源點可達的負權重環，不會自動列出所有受影響頂點或回傳負環本身。Lecture 12 的 graph-duplication 版本對負環 witness 與受影響頂點有更進一步的處理，可作為進階補充，不放在初學者主流程。

### 不建議放入主頁正文

- Lecture 12 的完整 `|V|+1` 層 graph duplication 建構細節與大型證明，對 beginner 主頁過於複雜；可在「進階閱讀」中簡述。
- 不將「Bellman-Ford 能處理負權重」誤寫成「可以在負權重環存在時仍產生有限且正確的所有最短距離」。
- 不把「偵測到負權重環」與「找到並列出整個負環」視為同一件事。

## 3. 與現有 Wiki 的重複內容比對

| 概念 | 現有頁面 | R5 建議處理 |
|---|---|---|
| Graph、Weighted Graph | 02 | 沿用 R1 定義，不重複 |
| Shortest Path Problem、SSSP | 03 | 沿用 R2 定義，僅簡短引用 |
| Edge Relaxation | 04 已在 Dijkstra 中使用，05 待建 | 09 說明 Bellman-Ford 如何重複鬆弛；一般定義統整在 05 |
| Dijkstra 非負權重限制 | 04 | 用來比較，不重寫 Dijkstra 頁面 |
| 負權重邊與負權重環 | 尚未詳細建立 | 新增至 09 |
| BFS | 08 待建 | 僅在比較時連結，不重複 |
| 時間複雜度 | 07 待建 | 提供 BF 的 `O(VE)`，完整比較交給 07 / 10 |

## 4. 技術正確性與教學注意事項

1. **負邊與負環不同**：Bellman-Ford 可處理負權重邊；有負權重邊不代表一定存在負權重環。
2. **負環的可達性重要**：標準單源 Bellman-Ford 偵測的是從來源點可達的負權重環。若負環與來源點完全不連通，就不影響該來源點的 SSSP 結果。
3. **`−∞` 的範圍要準確**：只有從來源點可達的負環，以及能由該負環繼續到達的頂點，其路徑成本才會無界下降；不能說所有頂點都會受影響。
4. **為什麼 `|V|-1` 輪**：沒有負權重環時，可取一條不重複頂點的最短路徑，至多使用 `|V|-1` 條邊。每輪會掃描所有邊；不同邊的掃描順序可能讓多段路徑在同一輪內傳播，因此不能把每輪距離估計簡單等同於「剛好使用 k 條邊」的距離。
5. **額外檢查輪不可省略**：在 `|V|-1` 輪後再掃描所有邊，若仍存在可改善的邊，且其起點距離有限，就可判定存在來源點可達的負權重環。
6. **不可達頂點防護**：實作時只在 `dist[u]` 有限時才計算 `dist[u] + w(u,v)`；如此避免把不可達頂點的無限大估計當成可更新路徑。
7. **無向負權重邊的特殊性**：若無向邊權重為負，來回走一次就形成總權重為負的封閉 walk（可視為負環）。MIT Lecture 12 因此把負權重討論限制在有向圖；初學者頁面需說清楚此點，不可讓讀者以為任意無向負邊可直接套用一般直覺。
8. **複雜度前提**：每輪掃描所有邊，最多 `|V|-1` 輪，再做一次檢查，因此時間複雜度為 `O(VE)`；`dist` 與 `prev` 輔助陣列為 `O(V)`，圖本身空間不計入。
9. **提前停止**：若完整一輪沒有更新，演算法已收斂，可以停止，不必執行剩餘輪次。
10. **和 Dijkstra 的比較**：Bellman-Ford 不用每輪取出目前距離最小頂點，也不需要 Priority Queue；它重複掃描所有邊。不要把兩者的控制流程混為一談。

## 5. 與其他 Wiki 頁面的關聯

- `wiki/03-shortest-path-problem.md`：沿用單源最短路徑問題定義。
- `wiki/04-dijkstra-algorithm.md`：比較 Dijkstra 與 Bellman-Ford 的權重限制及選擇策略。
- `wiki/05-edge-relaxation.md`：共用的距離鬆弛概念；Bellman-Ford 會反覆掃描全部邊。
- `wiki/07-time-complexity.md`：整理 `O(VE)` 與其他演算法的效率比較。
- `wiki/08-bfs.md`：比較無權重圖的 BFS。
- `wiki/10-algorithm-comparison.md`：統整 BFS、Dijkstra、Bellman-Ford 的適用條件。

## 6. 建議的 Ingest 範圍與目標頁面

- 主要頁面：`wiki/09-bellman-ford.md`（尚未建立）。
- 交叉更新：R3 / Dijkstra 頁面只需加入或確認連結，不重寫既有 Dijkstra 內容。
- 延伸連結：Edge Relaxation、Time Complexity、BFS、Algorithm Comparison。
- References：R5；若引用共通圖與 SSSP 定義，沿用 R1 / R2。

## 7. Review 結論

**Review 已由使用者核准，並已完成本地 Markdown Ingest。**

理由：MIT 6.006 Recitation 12 提供非常清楚的標準 Bellman-Ford 流程及 Python 實作，能支援 beginner 頁面；MIT 6.006 Lecture 12 則補充負權重環、simple shortest path 上界與進階 witness 分析。兩份來源搭配後，可以兼顧易懂的演算法流程與嚴謹的負環限制。

### 審查檢查表

- [x] 已確認 MIT 6.006 Lecture 12 的 Bellman-Ford 與負權重環內容。
- [x] 已確認 Recitation 12 的 `|V|-1` 輪鬆弛與額外負環檢查流程。
- [x] 已辨識 R5 與 R1、R2、R3 的重複內容並規劃去重。
- [x] 已區分負權重邊與負權重環。
- [x] 已標明標準 Recitation 版本只偵測可由 source 到達的負權重環，不直接輸出所有受影響節點。
- [x] 已說明不可達頂點需跳過鬆弛，且無向負邊的特殊問題。
- [x] 使用者核准。
- [x] 正式 Ingest：已建立 `wiki/09-bellman-ford.md`，並新增 `sources/R5.md`。
