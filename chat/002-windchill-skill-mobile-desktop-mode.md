# 002-windchill-skill-mobile-desktop-mode.md

## 日期
2025-12-11

## 任務名稱與背景
更新 windchill-dev Skill，加入手機模式與電腦模式的回報規則，確保在不同裝置情境下都能提供最適合的回報方式，並強制要求每次任務都必須產生 chat runlog。

## 修改檔案與路徑列表
- `skills/windchill-dev/SKILL.md`

## 主要變更內容
1. **新增「核心原則：永遠產生 Chat Runlog」章節**
   - 明確定義 runlog 為強制要求
   - 規範命名格式與必要內容（七大區塊）
   - 提供跨平台路徑說明

2. **新增「裝置模式切換」章節**
   - 定義手機模式觸發關鍵字
   - 定義電腦模式觸發關鍵字
   - 設定預設為電腦模式

3. **新增「手機模式（Mobile Mode）回報規則」章節**
   - 回覆原則：先寫 runlog，再回覆簡潔摘要
   - 回覆必須包含的六個項目
   - 格式限制：禁用寬表格、ASCII 藝術圖、Mermaid
   - 提供手機友善回覆範本

4. **新增「電腦模式（Desktop Mode）回報規則」章節**
   - 回覆原則：完整 runlog + 詳細摘要
   - 允許使用的格式：表格、程式碼區塊、ASCII 流程圖
   - 注意事項：大型內容應寫在 runlog
   - 提供電腦模式回覆範本

5. **更新「Runlog 完整格式範本」**
   - 加入 Git 資訊區塊（branch + commit hash）
   - 調整區塊順序以符合新規範

## 使用的 API / EXT / Portal / DB
- 無（本次為 Skill 文件更新）

## 測試步驟與結果
1. 檢查 SKILL.md YAML frontmatter 格式 → PASS
2. 驗證新增章節結構完整性 → PASS
3. 確認手機/電腦模式範本可複製使用 → PASS

## 風險與後續建議
- **風險**：使用者可能忘記指定裝置模式
  - **緩解**：已設定預設為電腦模式
- **建議**：
  - 未來可考慮加入自動偵測（如 User-Agent）
  - 可擴充更多裝置模式觸發關鍵字

## Git 資訊
- Branch: `claude/windchill-dev-skill-01XQzvuMXZPmQK6VS3ajZM4K`
- Commit: `a51a853`

## 待辦事項
- [ ] 在實際 windchillproject 中測試手機模式回報
- [ ] 收集使用者回饋調整格式限制
- [ ] 考慮新增「平板模式」支援
