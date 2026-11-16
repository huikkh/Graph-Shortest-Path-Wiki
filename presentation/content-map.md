# HTML 教學簡報：Wiki 知識對照表

簡報主題：從零理解 Dijkstra：如何找到兩個節點之間的最短路徑？

| 投影片 | 教學內容 | 使用的 Wiki 頁面 | 來源 ID |
|---|---|---|---|
| 01 | 封面與核心問題 | 01 Overview | R1–R6 |
| 02 | 學習目標與閱讀路線 | 01 Overview | R1–R6 |
| 03 | Graph、Vertex、Edge、有向／無向圖 | 02 Graph & Weighted Graph | R1 |
| 04 | Weighted Graph、路徑成本、最短路徑定義 | 03 Shortest Path Problem | R1、R2 |
| 05 | BFS、Dijkstra、Bellman-Ford 的選擇條件 | 03、07、10 | R2、R3、R5 |
| 06 | Dijkstra 的非負權重條件、Greedy、Settled | 04 Dijkstra Algorithm | R3 |
| 07 | dist、prev、settled、Priority Queue | 04 Dijkstra Algorithm | R3 |
| 08 | A–F 圖的六輪互動式 Dijkstra 執行 | 04 Dijkstra Algorithm | R3 |
| 09 | 最終距離、前驅、最短路徑樹 | 04 Dijkstra Algorithm | R3、R6 |
| 10 | Edge Relaxation 的候選距離與更新條件 | 05 Edge Relaxation | R3、R5 |
| 11 | Min Heap 操作及三種演算法複雜度 | 06、07 | R1–R5 |
| 12 | BFS 與 Bellman-Ford 的流程與適用情境 | 08、09、10 | R2、R3、R5 |
| 13 | 重點整理與互動小測驗 | 03–10 | R2–R5 |
| 14 | 六組核准來源 | 01 Overview | R1–R6 |

## 內容核對重點

- Dijkstra 範例沿用 Wiki 04 的無向加權圖：AB=4、AC=2、CB=1、BD=5、CD=8、CE=10、DE=2、DF=6、EF=3。
- 預期 settled 順序：A(0) → C(2) → B(3) → D(8) → E(10) → F(13)。
- 最終距離：A=0、B=3、C=2、D=8、E=10、F=13。
- A 到 F 路徑：A → C → B → D → E → F，總成本 13。
- Dijkstra 複雜度使用鄰接串列 + indexed Binary Min Heap 的 `O((V+E) log V)`；若改用 lazy deletion，不能直接沿用相同操作數分析。
- BFS 的最短路徑保證針對無權重或每條邊成本相同且非負的情況。
- Bellman-Ford 可處理負權重邊；來源可達的負權重環會令其可到達的後續頂點不存在有限最短距離。
- 路徑成本最低不等同邊數最少。

簡報使用內嵌 CSS、JavaScript 與 SVG，不需要外部函式庫或網路連線即可瀏覽。
