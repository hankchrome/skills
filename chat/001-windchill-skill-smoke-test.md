# 001-windchill-skill-smoke-test.md

## 日期
2025-12-11

## 問題描述
驗證新建立的 windchill-dev Skill 是否符合規範、內容完整，並能正常被 Claude Code 載入使用。

## 診斷過程

### 1. SKILL.md 格式驗證
- [x] YAML frontmatter 存在且格式正確
- [x] `name` 欄位：windchill-dev
- [x] `description` 欄位：完整描述 Windchill 開發用途

### 2. 內容完整性檢查
| 項目 | 狀態 | 說明 |
|------|------|------|
| 專案目錄結構 | ✅ | 包含 portal, wc-ext-java, chat, output, scripts 等 |
| 作業原則 | ✅ | 開始前必讀 CLAUDE.md、檢視 runlog、確認狀態 |
| EXT 開發規則 | ✅ | Java 後端修改注意事項、欄位同步更新 |
| Portal 開發規則 | ✅ | 前端修改、效能考量 |
| DB 修改規則 | ✅ | migration script、備份、索引設計 |
| Runlog 標準格式 | ✅ | 完整範本含所有必要區塊 |
| 回應格式建議 | ✅ | 問題診斷→設計→修改→測試→風險 |
| 常用指令 | ✅ | gradlew, npm, curl 範例 |

### 3. 常見痛點識別（基於 SKILL.md 內容）
根據 SKILL.md 中標記的 ⚠️ 警示區塊，識別出以下常見痛點：

1. **EXT 欄位不完整問題**
   - Entity 與 DTO 欄位對應不一致
   - OData $select 遺漏欄位
   - DB column 未建立

2. **Portal 查詢太慢問題**
   - 不必要的 OData 呼叫
   - 未使用 $select 限制欄位
   - N+1 查詢問題

3. **API 回傳資料不一致**
   - Cache 機制問題
   - Transaction isolation 設定
   - Lazy loading 行為

## 解決方案
本次為 Skill 初始建立的 smoke test，無需修改程式碼。確認 Skill 內容完整可用。

## 修改內容
- `skills/windchill-dev/SKILL.md`：新建立，181 行
- `chat/001-windchill-skill-smoke-test.md`：本檔案（smoke test runlog）

## 測試結果
| 測試項目 | 結果 |
|----------|------|
| YAML frontmatter 解析 | PASS |
| Markdown 格式正確性 | PASS |
| 必要區塊完整性 | PASS |
| 範例程式碼區塊語法 | PASS |

## 風險與注意事項
1. **目錄結構假設**：SKILL.md 中定義的目錄結構需與實際 windchillproject 一致，否則需調整
2. **指令相容性**：常用指令區塊假設使用 Gradle 和 npm，若專案使用其他工具需更新
3. **語言**：Skill 內容為中文，適用於中文團隊；若需英文版本需另行建立

## 待辦事項
- [ ] 將 Skill 安裝到實際 windchillproject 專案
- [ ] 根據實際專案結構調整目錄約定
- [ ] 補充專案特定的 OData endpoint 資訊
- [ ] 新增 BPM 開發相關細節（目前較少著墨）
- [ ] 考慮新增 Windchill REST API 整合指引
- [ ] 建立 EXT 欄位對照表範本
- [ ] 補充 Portal 元件開發最佳實踐

---

## 建議後續實作任務清單

基於 smoke test 識別的痛點，建議優先處理以下任務：

### 高優先級
1. **建立 EXT 欄位同步檢查機制**
   - 建立 Entity ↔ DTO ↔ OData ↔ DB 對照表
   - 撰寫驗證腳本自動檢查欄位一致性

2. **Portal 查詢效能優化指南**
   - 建立 OData 查詢最佳實踐文件
   - 加入 $select, $expand, $filter 使用範例

### 中優先級
3. **Runlog 自動化範本**
   - 建立 runlog 產生腳本
   - 自動帶入日期、git commit hash

4. **BPM 開發指引擴充**
   - 補充 BPM workflow 設計規範
   - 加入 BPM 除錯技巧

### 低優先級
5. **多語言支援**
   - 建立英文版 SKILL.md
   - 支援切換語言

---

*本 runlog 由 windchill-dev Skill smoke test 自動產生*
