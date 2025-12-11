---
name: windchill-dev
description: 專門處理 AAEON Windchill（Portal / EXT / OData / BPM）開發與除錯的 Skill。適用於 Windchill 客製化模組的設計、實作、除錯與維護工作。
---

# Windchill Development Skill

本 Skill 專為 AAEON Windchill 專案開發而設計，涵蓋 Portal、EXT（Extension）、OData、BPM 等模組的開發與除錯工作。

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
├── chat/                   # Claude 對話紀錄與 runlog
│   └── 0xx-*.md            # 依序編號的工作紀錄
├── output/                 # 產出檔案（報表、匯出資料等）
├── scripts/                # 自動化腳本（部署、測試等）
├── docs/                   # 專案文件
└── CLAUDE.md               # 專案 Claude 指引（必讀）
```

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

## Database 修改規則

- 所有 Schema 變更需記錄於 migration script
- 修改前先備份相關資料
- 注意索引設計以維持查詢效能
- 避免直接修改 Windchill 系統表

## Runlog 標準格式

每次重大修改後，需在 `chat/` 目錄產出 runlog：

```markdown
# 0xx-{簡短描述}.md

## 日期
YYYY-MM-DD

## 問題描述
[描述遇到的問題或需求]

## 診斷過程
[分析步驟與發現]

## 解決方案
[採用的解決方式]

## 修改內容
- 檔案 A：描述變更
- 檔案 B：描述變更

## 測試結果
[驗證方式與結果]

## 風險與注意事項
[潛在影響與後續建議]

## 待辦事項
- [ ] 項目 1
- [ ] 項目 2
```

## 回應格式建議

處理 Windchill 開發問題時，建議依以下結構回應：

### 1. 問題診斷
- 症狀描述
- 可能原因分析
- 需要確認的資訊

### 2. 設計方案
- 方案選項（如有多種）
- 建議方案與理由
- 影響範圍評估

### 3. 修改內容
- 變更檔案清單
- 具體程式碼修改
- 設定檔調整

### 4. 測試方式
- 單元測試（如適用）
- 整合測試步驟
- 手動驗證項目

### 5. 風險與建議
- 潛在副作用
- 效能影響
- 後續優化建議

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

## 參考資源

- Windchill 官方文件
- 專案 CLAUDE.md
- chat/ 目錄下的歷史 runlog
