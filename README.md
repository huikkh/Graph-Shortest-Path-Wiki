# 圖論與最短路徑演算法 — LLM Wiki 與 Dijkstra 教學簡報

本專案以 Markdown 建立教學型 LLM Wiki，並以 Wiki 作為知識來源製作 14 張互動式 Dijkstra HTML 簡報。目標讀者為沒有學過圖論演算法的初學者。

## 快速開始

- **瀏覽教學簡報：** 開啟本專案的 `index.html`，或直接開啟 [`presentation/index.html`](presentation/index.html)。
- **閱讀 Wiki：** 從 [`wiki/01-overview.md`](wiki/01-overview.md) 開始。
- **查看資料來源：** `sources/` 保存 R1–R6 的來源摘要、擷取範圍及 Review 紀錄。
- **查看 Workflow：** [`workflow/loop-graph-and-reflection.md`](workflow/loop-graph-and-reflection.md) 說明整體 Loop / Graph、檢查節點與反思。

## Wiki 頁面（10 頁）

| 編號 | 頁面 | 主要內容 |
|---|---|---|
| 01 | [Overview](wiki/01-overview.md) | 學習目標、閱讀路線、知識地圖與來源管理 |
| 02 | [Graph & Weighted Graph](wiki/02-graph-weighted-graph.md) | 頂點、邊、權重、有向／無向圖、圖表示法 |
| 03 | [Shortest Path Problem](wiki/03-shortest-path-problem.md) | 最短路徑定義、查詢類型與演算法選擇 |
| 04 | [Dijkstra Algorithm](wiki/04-dijkstra-algorithm.md) | Greedy、Frontier／Settled、不變量、最短路徑樹與路徑還原 |
| 05 | [Edge Relaxation](wiki/05-edge-relaxation.md) | 候選距離、`dist`／`prev` 更新、Dijkstra／Bellman-Ford |
| 06 | [Priority Queue](wiki/06-priority-queue.md) | Priority Queue ADT、Binary Heap、Min Heap 操作 |
| 07 | [Time Complexity](wiki/07-time-complexity.md) | BFS、Dijkstra、Bellman-Ford 的時間與空間成本 |
| 08 | [BFS](wiki/08-bfs.md) | FIFO Queue、逐層探索、非加權圖最短路徑 |
| 09 | [Bellman-Ford](wiki/09-bellman-ford.md) | 負權重、重複鬆弛、負環偵測 |
| 10 | [Algorithm Comparison](wiki/10-algorithm-comparison.md) | 三種演算法的適用條件、策略與複雜度 |

## 專案目錄

```text
.
├── index.html                     # GitHub Pages 首頁，導向教學簡報
├── README.md
├── ingest-log.md                  # 各次 Review / Ingest 紀錄
├── local-check-report.md          # 本地品質檢查紀錄
├── wiki/                          # 10 個 Markdown 知識頁面
├── sources/                       # R1–R6 來源與 Review 紀錄
├── presentation/
│   ├── index.html                 # 14 張互動式 HTML 簡報
│   ├── content-map.md             # 投影片與 Wiki 頁面／來源對照
│   └── README.md                  # 簡報操作說明
└── workflow/
    ├── html-slide-workflow.md     # Query、Generate、測試紀錄
    └── loop-graph-and-reflection.md # 整體流程圖與反思
```

## 外部資料來源

- **R1 — Cornell CS 2110, Graphs**：Graph、Vertex、Edge、圖的種類、Path、Weight、Adjacency Matrix、Adjacency List。
- **R2 — NetworkX, Shortest Paths**：最短路徑定義、加權與非加權圖、查詢範圍及演算法選擇。
- **R3 — Cornell CS 2110, Shortest Paths**：Dijkstra 非負權重條件、Frontier／Settled、不變量、距離與前驅紀錄。
- **R4 — Cornell CS 2110, Heaps and Priority Queues**：Priority Queue ADT、Binary Heap、Min Heap 與操作。
- **R5 — MIT 6.006, Bellman-Ford Lecture / Recitation**：重複鬆弛、負權重環檢查與複雜度。
- **R6 — Cornell CS 312, Finding Shortest Paths**：補充 Dijkstra 的 shortest-path tree 與 predecessor property。

詳細網址及擷取範圍見 `sources/`。

## GitHub Pages 部署

1. 將本專案全部檔案與資料夾上傳到 GitHub Repository 的根目錄（不要只上傳 ZIP）。
2. 開啟 Repository 的 **Settings → Pages**。
3. 在 **Build and deployment** 選擇 **Deploy from a branch**。
4. Branch 選 `main`，資料夾選 `/(root)`，按 **Save**。
5. 等 GitHub Pages 完成部署後，開啟顯示的網站網址。根目錄的 `index.html` 會導向 `presentation/index.html`。

若只需在電腦本機展示，解壓縮後以 Chrome 或 Edge 開啟 `presentation/index.html` 即可。簡報的 CSS 與 JavaScript 已內嵌，不需要額外套件。

## Workflow 與品質檢查

- 外部資料採 Review → Approval → Ingest → Local Checks 流程。
- Wiki 的中繼資料、內部連結、來源紀錄與範例計算有本地檢查紀錄。
- HTML 內容與來源對照見 `presentation/content-map.md`。
- HTML 生成、全螢幕修正及瀏覽器測試紀錄見 `workflow/html-slide-workflow.md`。
- 完整 Loop / Graph 與實際反思見 `workflow/loop-graph-and-reflection.md`。

## 狀態與限制

- Wiki 知識頁面：10 / 10。
- 已核准並整理的外部來源：6 篇（R1–R6）。
- 教學簡報：14 張互動式 HTML 投影片。
- 本專案是本地 Markdown Wiki，不代表已寫入外部 LLM Wiki 服務。Local Check 不等同外部 Wiki 引擎的 Lint；外部引擎未連線，因此不宣稱已執行外部 Lint。


## 最新簡報修正（2026-11-16）
第 11 頁複雜度分析說明已移入右側複雜度卡片，避免與卡片列重疊。請使用 `presentation/index.html`。
