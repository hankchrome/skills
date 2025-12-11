# 003-windchill-skill-safety-guard-rails.md

## 日期
2025-12-11

## 任務名稱與背景
在 windchill-dev Skill 中新增「安全保護規則（Safety Guard Rails）」章節，確保 Claude 在執行 Windchill 相關任務時有明確的安全邊界，避免誤操作造成資料損失或系統問題。

## 修改檔案與路徑列表
- `skills/windchill-dev/SKILL.md`

## 主要變更內容

### 新增「安全保護規則（Safety Guard Rails）」章節，包含四大子規則：

1. **矛盾偵測（Conflict Detection）**
   - 定義五種禁止直接執行的矛盾情況
   - 要求明確指出衝突點、提供可能方向、等待確認
   - 提供範例回應格式

2. **破壞性操作 Double-Confirm**
   - 檔案操作：刪除、移動、覆蓋
   - 程式碼修改：EXT Java、Portal API、SQL/DB schema、大量 diff
   - Windchill 物件：Part/Doc/BOM/ECN 的 CRUD
   - 企業資料：ERP、PLM、BPM、Salesforce
   - 提供確認對話範例

3. **敏感資訊保護**
   - 禁止輸出：連線字串、API 金鑰、IP、個資
   - 提供遮罩格式範例

4. **回滾準備**
   - 記錄原始狀態
   - 提供回滾步驟
   - 確認 Git 狀態
   - 提供範例格式

## 使用的 API / EXT / Portal / DB
- 無（本次為 Skill 文件更新）

## 測試步驟與結果
1. 檢查 SKILL.md 結構完整性 → PASS
2. 驗證新章節位置（在核心原則與裝置模式之間） → PASS
3. 確認範例格式可直接複製使用 → PASS

## 風險與後續建議
- **風險**：
  - 過於嚴格的 double-confirm 可能影響工作效率
  - 緩解：僅針對破壞性操作要求確認，一般查詢與讀取不受影響

- **建議**：
  - 可根據實際使用情況調整 30 行 diff 的閾值
  - 考慮新增「信任模式」讓使用者可暫時跳過確認（需明確聲明）

## Git 資訊
- Branch: `claude/windchill-dev-skill-01XQzvuMXZPmQK6VS3ajZM4K`
- Commit: `0d16fdd`

## 待辦事項
- [ ] 在實際 windchillproject 中測試矛盾偵測行為
- [ ] 收集使用者對 double-confirm 流程的回饋
- [ ] 評估是否需要新增「信任模式」
