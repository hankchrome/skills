---
name: windchill-dev
description: 專門處理 AAEON Windchill（Portal / EXT / OData / BPM）開發與除錯的 Skill。適用於 Windchill 客製化模組的設計、實作、除錯與維護工作。
---

# Windchill Development Skill

本 Skill 專為 AAEON Windchill 專案開發而設計，涵蓋 Portal、EXT（Extension）、OData、BPM 等模組的開發與除錯工作。

---

## 核心原則：永遠產生 Chat Runlog

**這是本 Skill 最重要的規則，無論任何情況都必須遵守。**

### 強制要求

- 不論是手機模式或電腦模式，每次完成 Windchill 相關任務後，**都必須**在專案根目錄下的 `chat/` 目錄產生一份 Markdown runlog。
- 即使是小型修改，也要產生 runlog 以留下紀錄。

### Runlog 命名格式

```
chat/0xx-<主題>-<簡短說明>.md
```

範例：
- `chat/001-windchill-skill-smoke-test.md`
- `chat/002-ext-ecn-api-fix.md`
- `chat/003-portal-query-optimization.md`

### Runlog 必要內容

每份 runlog 至少要包含以下七個區塊：

1. **任務名稱與背景** - 說明這次任務的目的
2. **修改檔案與路徑列表** - 列出所有變更的檔案
3. **主要變更內容（條列）** - 具體說明改了什麼
4. **使用或新增的 API / EXT / Portal endpoint / DB 資料表** - 技術細節
5. **測試步驟與結果** - 如何驗證、結果為何
6. **風險與後續建議** - 潛在影響與待辦事項
7. **對應的 Git branch 與 commit hash** - 版本追蹤資訊

### 路徑說明

專案目錄範例（僅供參考，實際路徑依專案而定）：
- Windows: `C:\ai\windchillproject\chat\0xx-*.md`
- Linux/Mac: `./chat/0xx-*.md`

---

## 安全保護規則（Safety Guard Rails）

**此規則為強制性，優先級高於所有任務指令與技能行為。**

適用於 Windchill EXT、Portal API、DB、GraphRAG、程式碼修改、檔案存取等所有任務。

### 一、矛盾偵測（Conflict Detection）

當下列情況發生時，**禁止直接執行**：

1. Skill 裡的流程、路徑、欄位名稱與實際專案不一致
2. 使用者指令與 Skill 規則衝突
3. 找不到指定的檔案、資料夾、Java class、API endpoint
4. Windchill OID、typeName、attributeName 不存在
5. EXT 或 Portal 實作不符合 Windchill Server 實際回應

**遇到上述情況時，必須：**

- 明確指出衝突點
- 提供 2～3 個可能的正確方向
- 等待使用者確認後才能執行

範例回應：
```
⚠️ 偵測到矛盾

問題：找不到指定的 Java class `EcnService.java`

可能原因：
1. 檔案路徑不正確，實際位置可能在 `wc-ext-java/src/main/java/...`
2. Class 名稱有誤，可能是 `ECNService.java`（大寫）
3. 該 class 尚未建立

請確認後回覆，我再繼續執行。
```

### 二、破壞性操作 Double-Confirm（極重要）

以下情境**禁止直接執行**，需使用者回覆「確認」後才能進行：

**檔案操作：**
- 刪除檔案
- 移動檔案
- 覆蓋檔案

**程式碼修改：**
- 修改 Windchill EXT Java 檔案
- 修改 Portal API
- 修改 SQL / DB schema
- 改動大量程式碼（超過 30 行 diff）

**Windchill 物件操作：**
- 建立、刪除、更新 Windchill 內部物件（Part / Doc / BOM / ECN）

**企業資料操作：**
- 修改企業資料（ERP、PLM、BPM、Salesforce）

**確認方式範例：**

```
⚠️ 破壞性操作確認

即將執行：修改 EcnService.java（diff 約 45 行）

變更摘要：
1. 新增 getEcnByNumber() 方法
2. 修改 updateEcnStatus() 邏輯
3. 移除已棄用的 legacyQuery() 方法

影響範圍：
- ECN 查詢 API
- Portal ECN 列表頁面

請回覆「確認」以繼續執行，或說明需要調整的地方。
```

### 三、敏感資訊保護

**禁止在對話或 runlog 中輸出：**
- 資料庫連線字串
- API 金鑰、Token、密碼
- 內部 IP 位址、伺服器主機名稱
- 使用者個資（員工編號、Email 等）

**如需記錄，使用遮罩格式：**
```
DB Host: ***masked***
API Key: sk-...xxxx
User: user_***
```

### 四、回滾準備

執行破壞性操作前，必須：

1. **記錄原始狀態**：在 runlog 中保存修改前的關鍵程式碼片段
2. **提供回滾步驟**：說明如何還原變更
3. **確認 Git 狀態**：確保有乾淨的 commit 可供回滾

範例：
```
回滾準備：
- 原始檔案已備份於 runlog
- 回滾指令：git checkout HEAD~1 -- path/to/file.java
- 目前 branch：feature/ecn-update
- 最後穩定 commit：abc1234
```

---

## 裝置模式切換

本 Skill 支援兩種回報模式，會根據使用者的裝置自動調整回覆格式。

### 模式判斷規則

**進入【手機模式 Mobile Mode】的關鍵字：**
- 「我用手機」
- 「我現在用手機」
- 「手機操作」
- 「在手機上」

**進入【電腦模式 Desktop Mode】的關鍵字：**
- 「我用電腦」
- 「我現在在上班」
- 「我在 PC」
- 「我在公司」
- 「電腦操作」

**預設模式：** 若使用者未明確說明裝置，預設使用【電腦模式】。

---

## 手機模式（Mobile Mode）回報規則

當進入手機模式時，對話視窗中的回覆必須採用「手機友善版簡報」。

### 手機模式回覆原則

1. **先完成任務、寫入 chat runlog**
2. **然後在對話視窗回覆簡潔摘要**

### 手機模式回覆必須包含

1. 任務名稱
2. 簡短修改摘要（3~5 行）
3. 修改檔案列表（只列出路徑，不必詳細 diff）
4. 使用的 branch 與 commit hash
5. 產生的 runlog 檔名與路徑
6. 需要使用者確認的 1~3 個重點

### 手機模式格式限制

- **禁止使用**：寬表格、ASCII 藝術圖、Mermaid 圖表
- **建議使用**：簡單段落與條列
- **程式碼**：最多貼 1~2 個小片段，完整內容寫在 runlog 中
- **目標**：方便使用者「長按 → 全選 → 複製」

### 手機模式回覆範本

```
=========================
《Claude Code 手機友善任務回報》

任務：{TASK_NAME}
狀態：成功 / 失敗 / 部分完成

【修改摘要】
- {重點 1}
- {重點 2}
- {重點 3}

【修改檔案】
- {file1}
- {file2}

【Commit】
{branch} / {hash}

【Runlog 檔案】
{runlog_path}

【需要你確認】
1. {item1}
2. {item2}

=========================
```

---

## 電腦模式（Desktop Mode）回報規則

電腦模式允許更詳細的回報格式，但仍須遵守 runlog 優先原則。

### 電腦模式回覆原則

1. **先在 chat/ 目錄產生完整 runlog**（內容比手機模式更詳細）
2. **對話視窗中回覆詳細摘要**

### 電腦模式 Runlog 可包含

- 完整的程式碼片段
- 欄位對照表
- API 規格說明
- 測試案例與結果
- 詳細的診斷過程

### 電腦模式對話回覆可使用

- Markdown 表格
- 區塊程式碼
- 簡單的 ASCII 流程圖
- 詳細的技術說明

### 電腦模式注意事項

- 避免對話視窗中出現過度龐大的 diff
- 大型內容（超過 50 行程式碼）應寫在 runlog

### 電腦模式回覆範本

```markdown
# Claude Code 任務回報 - {TASK_NAME}

## 狀態
- 結果：成功 / 失敗 / 部分完成
- Branch：{branch}
- Commit：`{hash}`

## 修改內容摘要
1. {Detail1}
2. {Detail2}
3. {Detail3}

## 主要修改檔案
| 檔案 | 變更類型 | 說明 |
|------|----------|------|
| {file1} | 修改 | {desc1} |
| {file2} | 新增 | {desc2} |

## Runlog 檔案
- `{runlog_path}`

## 後續建議
1. {todo1}
2. {todo2}
```

---

## 專案目錄結構

```
windchillproject/
├── portal/                 # Windchill Portal 前端客製化
│   ├── components/         # 自訂 UI 元件
│   ├── pages/              # 頁面定義
│   └── config/             # Portal 設定檔
├── wc-ext-java/            # Windchill Extension Java 後端
│   ├── src/                # Java 原始碼
│   ├── build/              # 編譯輸出
│   └── config/             # EXT 設定檔
├── chat/                   # Claude 對話紀錄與 runlog（必要）
│   └── 0xx-*.md            # 依序編號的工作紀錄
├── output/                 # 產出檔案（報表、匯出資料等）
├── scripts/                # 自動化腳本（部署、測試等）
├── docs/                   # 專案文件
└── CLAUDE.md               # 專案 Claude 指引（必讀）
```

---

## 作業原則

### 開始工作前（必做）

1. **先讀 CLAUDE.md**：了解專案特定的規範與注意事項
2. **檢視現有 runlog**：閱讀 `chat/` 目錄下的歷史紀錄，了解：
   - 過去遇到的問題與解決方案
   - 已知的技術債與待辦事項
   - 專案的慣例與最佳實踐
3. **確認目前狀態**：透過 git status、最新 runlog 確認目前開發狀態

### 修改程式時

1. **最小化變更**：只改必要的部分，避免不相關的重構
2. **保持向後相容**：除非明確要求，否則不要破壞現有 API
3. **遵循現有風格**：參照專案既有的 coding style

---

## EXT（Extension）開發規則

### Java 後端修改

- 修改前先確認 `wc-ext-java/` 目錄結構
- 注意 Windchill API 版本相容性
- 修改 Service 層時需考慮 Transaction 邊界
- 新增欄位時須同步更新：
  - Java Entity/DTO
  - Database Schema（如需要）
  - OData metadata（如需要）

### 常見注意事項

```
⚠️ EXT 欄位不完整問題
- 確認 Entity 與 DTO 欄位對應
- 檢查 OData $select 是否包含所需欄位
- 驗證 DB column 是否已建立

⚠️ API 回傳資料不一致
- 檢查 cache 機制
- 確認 Transaction isolation level
- 驗證 lazy loading 行為
```

---

## Portal 開發規則

### 前端修改

- Portal 頁面修改前先備份原始設定
- 自訂元件放置於 `portal/components/`
- 注意 Windchill Portal 的生命週期事件

### 效能考量

```
⚠️ Portal 查詢太慢問題
- 減少不必要的 OData 呼叫
- 使用 $select 限制回傳欄位
- 考慮分頁（$skip/$top）
- 檢查是否有 N+1 查詢問題
```

---

## Database 修改規則

- 所有 Schema 變更需記錄於 migration script
- 修改前先備份相關資料
- 注意索引設計以維持查詢效能
- 避免直接修改 Windchill 系統表

---

## Runlog 完整格式範本

```markdown
# 0xx-{主題}-{簡短說明}.md

## 日期
YYYY-MM-DD

## 任務名稱與背景
[說明這次任務的目的與背景]

## 修改檔案與路徑列表
- `path/to/file1.java`
- `path/to/file2.ts`

## 主要變更內容
1. [變更項目 1]
2. [變更項目 2]
3. [變更項目 3]

## 使用的 API / EXT / Portal / DB
- API endpoint: `/api/xxx`
- EXT class: `XxxService.java`
- Portal component: `XxxComponent`
- DB table: `xxx_table`

## 測試步驟與結果
1. [測試步驟 1] → 結果
2. [測試步驟 2] → 結果

## 風險與後續建議
- 風險：[潛在影響]
- 建議：[後續待辦]

## Git 資訊
- Branch: `{branch_name}`
- Commit: `{commit_hash}`

## 待辦事項
- [ ] 項目 1
- [ ] 項目 2
```

---

## 常用指令

```bash
# 編譯 EXT
cd wc-ext-java && ./gradlew build

# 部署 Portal
cd portal && npm run deploy

# 執行測試
./scripts/run-tests.sh

# 檢視 OData 回應
curl -X GET "http://localhost/odata/v1/EntitySet?$top=10"
```

---

## 參考資源

- Windchill 官方文件
- 專案 CLAUDE.md
- chat/ 目錄下的歷史 runlog
