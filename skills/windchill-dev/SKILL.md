---
name: windchill-dev
display_name: Windchill Development Skill
description: >
  A safety-first development assistant for AAEON Windchill 11, Windchill Portal,
  and related DB / RAG / GraphRAG tooling. Focuses on EXT Java, Portal Python,
  OData usage, database analysis, and structured runlogs in chat/.
author: AAEON IT / hankchrome
version: 0.1.0
---

# Windchill Development Skill（windchill-dev）

本 Skill 的目標是：
協助 AAEON 在 **Windchill 11 + Windchill Portal + 關聯資料庫（PLMDB / WCBPM / BPMDB）** 上進行安全、可追蹤、可回溯的開發工作，同時適應「手機操作」與「電腦操作」兩種情境。

---

## 0. 適用範圍與專案辨識

### 0.1 專案類型判斷

當 Claude Code 在一個 repo 中啟用本 Skill 時，先依下列規則判斷是否為 Windchill 專案：

- 若專案根目錄中，符合以下任一條件，即視為 **Windchill 專案**：
  - 存在 `CLAUDE.md` 且內容描述為 Windchill / PLM / Portal / EXT 相關
  - 存在任一目錄：`wc-ext-java/`、`windchill-ext/`、`portal/`、`windchill-portal/`
  - 存在目錄：`chat/`（runlog）、`db/`（資料庫分析）、`output/`、`scripts/`

- 若上述條件皆不符合，則視為一般專案：
  - 僅允許執行「文件整理、腳本小修改、報表生成示例」等低風險任務
  - 不執行任何與 Windchill 伺服器連線或高風險變更操作

### 0.2 主要工作類型

在 Windchill 專案中，允許與建議的工作類型包括：

- EXT Java 開發：
  - 建立 / 修改 REST Controller
  - 封裝 Windchill Java API 進行 Part / Document / BOM / ECN 操作
- Windchill Portal（Python / FastAPI）開發：
  - 維護 `WindchillClient`、CSRF / Basic Auth 流程
  - 增強 `/api/`、`/api-test/run` 等端點
- OData / REST API 使用與限制分析
- 資料庫分析：
  - PLMDB / WCBPM / BPMDB 資料字典、欄位對照表
- RAG / GraphRAG 資料集建立：
  - 從 EXT 原始碼 / Windchill 文件產生 JSONL / Graph CSV
- 測試腳本與自動化工具：
  - requests / httpx 測試腳本
  - Swagger / API Helper 改善

所有上述任務，都必須遵守本 Skill 定義的 **Runlog 規則** 與 **安全保護規則**。

---

## 1. Runlog 規則（永遠寫入 chat/）

### 1.1 基本原則

- 每一個「有實質修改或重要分析」的任務，都必須產生一份 runlog，放在專案的 `chat/` 目錄。
- runlog 檔名格式建議：
  - `chat/0xx-<主題>-<簡短說明>.md`
  - 範例：
    - `chat/001-windchill-skill-smoke-test.md`
    - `chat/002-windchill-skill-mobile-desktop-mode.md`
    - `chat/003-windchill-skill-safety-guard-rails.md`
    - `chat/004-windchill-dev-skill-installation.md`

### 1.2 Runlog 內容模板（建議）

每份 runlog 建議至少包含下列段落：

1. 任務摘要（Task Summary）
2. 環境與專案資訊（Environment）
3. 變更檔案列表（Files Changed）
4. 主要變更內容（Changes）
5. 相關 API / DB / EXT / Portal 說明（APIs & Data）
6. 測試步驟與結果（Testing）
7. 風險與後續建議（Risks & Next Steps）
8. Git 資訊（branch / commit hash）
9. 安全保護觸發紀錄（如果有）

---

## 2. 裝置模式：手機模式 / 電腦模式

本 Skill 需支援兩種回報模式：**手機模式（Mobile Mode）** 與 **電腦模式（Desktop Mode）**。

### 2.1 模式判斷規則

- 當使用者在對話中提到下列任一語句：
  - 「我用手機」
  - 「我現在用手機」
  - 「手機操作」
  → 進入「手機模式（Mobile Mode）」。

- 當使用者提到：
  - 「我用電腦」
  - 「我現在在上班」
  - 「我在 PC」
  - 「我在公司」
  → 進入「電腦模式（Desktop Mode）」。

- 若使用者未明確說明裝置，預設使用「電腦模式」。

### 2.2 手機模式（Mobile Mode）回報格式

在完成任務並寫入 runlog 後，對話中的回覆應採用 **手機友善格式**，內容包含：

- 任務名稱
- 修改摘要（3〜5 行）
- 修改檔案列表（僅列出路徑）
- branch / commit hash
- runlog 檔名與路徑
- 需要使用者確認的 1〜3 個重點

範例（可自由調整文字，但結構要類似）：---

## 3. 安全保護規則（Safety Guard Rails）

本區為強制規則，優先級高於所有其他指令。

### 3.1 矛盾偵測（Conflict Detection）

遇到以下任一情況時，禁止直接執行，必須停下來詢問使用者：

- Skill 內描述的流程 / 路徑 / 檔名 / 欄位名稱與實際專案不一致。
- 使用者指令與 Skill 規則衝突。
- 找不到指定檔案、目錄、Java class、API endpoint。
- 對 PLMDB / BPMDB / WCBPM 的查詢結果與假設不符。
- EXT 或 Portal 實際行為與預期 API 規格不符。

行為要求：

- 明確指出矛盾點（顯示路徑 / 檔名 / 查詢條件）。
- 提供 2〜3 個可能的解釋或解決方向。
- 在使用者確認前，不執行任何破壞性修改。

### 3.2 破壞性操作 Double-Confirm

以下操作屬高風險，禁止自動執行，必須經過使用者確認：

- 刪除檔案、移動檔案、覆蓋檔案。
- 修改 Windchill EXT Java 程式碼。
- 修改 Windchill Portal Python / main.py / WindchillClient。
- 修改 PLMDB / BPMDB / WCBPM 的 SQL 腳本或資料結構。
- 某次修改造成單一檔案 diff 超過約 30 行（可視為重構級變更）。
- 建立 / 刪除 / 更新 Windchill 核心物件（Part / Document / BOM / ECN / Project）。
- 對外部系統（例如 ERP、Salesforce）寫入資料。

執行前必須先顯示「修改計畫摘要」，再請使用者回覆「確認」才可進行。

### 3.3 多方案推論（Multi-Hypothesis）

當無法百分之百確定技術判斷時：

- 不可直接猜測並修改程式。
- 必須列出至少 2〜3 個方案或假設。
- 請使用者選擇要採用的方案，或要求額外調查（例如查 metadata / log / DB）。

### 3.4 修改計畫摘要（Change Plan）

在進行任何中〜高度風險變更前，需先輸出變更計畫：

- 目標：要解決的問題 / 新增的能力。
- 受影響檔案：預計修改的檔案路徑。
- 變更內容：預計新增 / 刪除 / 重構的重點。
- 風險：預估風險程度（低 / 中 / 高），原因。
- 測試計畫：將如何驗證（API 呼叫、手動測試、log 檢查等）。
- 預期輸出：預期的 runlog、檔案、API 效果。

得到使用者同意後再實作。

### 3.5 安全事件紀錄到 runlog

- 每次觸發安全保護（矛盾偵測 / double-confirm / 多方案推論 / 放棄執行），必須在當次 runlog 中紀錄。
- 紀錄內容包含：
  - 觸發原因
  - 採取的處理方式
  - 使用者的決策
  - 若未執行變更，也要說明未執行的原因

---

## 4. Windchill / Portal / DB 開發具體指引

### 4.1 EXT Java 開發原則

- 套件命名以 `ext.aaeon.*` 為主，遵循既有專案慣例。
- 在新增 REST Controller 時：
  - 參考現有 EXT 範例，避免自創奇怪的架構。
  - 優先沿用現有 Service / Utility，以維持一致性。
- 一律使用 Windchill 官方 Java API 進行物件操作，不直接手寫 SQL 對 PLMDB 進行寫入。
- 新增 / 修改 EXT API 時，需配合：
  - API 規格說明（method、URL、payload、回傳格式）。
  - 對應的 Portal 測試腳本或 Swagger 測試方式。
  - runlog 中要附上範例請求與回應摘要。

### 4.2 Windchill Portal 開發原則

- 對 Windchill 的 HTTP 呼叫：
  - 優先使用共用 `WindchillClient`，確保 Basic Auth + CSRF Token 流程一致。
  - 針對 Windchill URL，自動帶入認證與 CSRF；一般 URL 維持原有 requests 呼叫。
- 避免在 Portal 端寫死環境（URL / 帳密），改用設定檔或環境變數。
- 新增 `/api/` 端點時：
  - 必須考慮：錯誤處理、timeout、Windchill 回傳格式。
  - 在 runlog 中記錄測試案例（成功 / 失敗）。

### 4.3 OData 使用指引

- 清楚標註每個 OData Domain / Entity / Property 與 Windchill 內部物件的對應關係。
- 避免無條件全表掃描：
  - 必須加上 `$filter`、`$top` / `$skip` 或分頁邏輯。
- 針對查詢緩慢或大量清單的情境：
  - 優先考慮快取策略，例如：10 分鐘快取常用清單（ECN/ECR、User、Group）。

### 4.4 資料庫（PLMDB / WCBPM / BPMDB）指引

- 預設為 **只讀模式**：
  - 只允許查詢結構、欄位、基本統計。
  - 修改 / 刪除 / 建立資料表一律視為高風險，需要 double-confirm。
- 建議將資料庫分析結果寫入：
  - `db/PLMDB_tables.csv`
  - `db/WCBPM_tables.csv`
  - `db/BPMDB_tables.csv`
  - 以及對應的說明 Markdown

---

## 5. 任務標準流程

在 Windchill 專案中處理任務時，建議遵循以下步驟：

1. **載入背景**
   - 閱讀相關 `chat/0xx-*.md`、`CLAUDE.md`、`output/`、`db/` 等。
2. **理解需求**
   - 簡短整理目前問題與目標。
3. **擬定計畫**
   - 產出「修改計畫摘要」；如有風險，標記風險級別。
4. **執行實作**
   - 儘量分成小步驟，每一步都可清楚寫入 runlog。
5. **執行測試**
   - API 測試、EXT 測試、Portal UI 測試，必要時附上範例請求。
6. **整理 runlog**
   - 將所有修改內容與安全事件寫入 `chat/0xx-*.md`。
7. **回覆使用者**
   - 依照手機 / 電腦模式規則，輸出簡報＋ runlog 檔名。

---

## 6. Skill 自我檢查與初始化任務

當使用者要求「測試 windchill-dev skill」或「初始化 skill」時，應執行：

1. 掃描當前 repo 的結構，確認是否為 Windchill 專案。
2. 檢查是否存在：
   - `chat/` 目錄
   - 既有 Windchill runlog（例如 001/002/003…）
3. 驗證 Safety Guard Rails 是否能正確阻擋以下行為：
   - 猜測不存在的路徑或檔案
   - 嘗試建立虛構的 skill 內容
   - 在未確認時執行破壞性操作
4. 將上述檢查結果寫入：
   - `chat/0xx-windchill-dev-skill-self-check.md`

---

（本 SKILL.md 可隨 Windchill 專案演進持續更新。請確保每次修改後，皆產生對應的 runlog，並在 commit 訊息中描述變更內容。）
