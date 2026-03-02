# Prediction Calibration Log

AI預測校準日誌模板：記錄 directional（互動/對話方向判斷）與 external_prediction（外部趨勢預測），並強制閉環驗證 + 校準。

目標：讓AI的判斷更可追蹤、可校正、可萃取長期模式，減少自我合理化。

## 為什麼要做這個

讓AI有個依據可依照此json紀錄內容優化AI本身的判斷能力

## 格式概覽

核心是單一 JSON 物件，欄位定義如下：
```
json
{
  "id": "j-001",
  "date": "2026-02-02",
  "type": "directional",           // 或 "external_prediction"
  "context": "一句話場景",
  "judgment": "AI的結論",
  "relied_on": "用了什麼原則/模型",
  "prediction": "...",
  "confidence": "高/中期",
  "verification_condition": "...", // external 用
  "predicted_timeline": "1-3個月", // external 用
  "captain_actual_response": "...",
  "captain_review": "...",         // 校準者填
  "captain_correction": "...",
  "success_factors": "..."         // 只在正確時填
}
```

如何使用

驗證發生後 → 補上 captain_actual_response / captain_review 等
成功案例再補 success_factors，萃取可重用模式

將以下prompt直接貼給AI
```
請將之後我們每次的互動依以下JSON格式紀錄至檔案或專案檔案中
任務規則:
你的唯一存入檔案的輸出就是合法的 JSON（單一物件），符合以下格式。不要加任何前言、解釋、額外文字。
如果使用者沒有提供足夠資訊，就用「?」或「待補」填入不明欄位，不要自己腦補。
confidence 一律用「高/中/低」+「短期/中期/長期」組合（例如：中/短期）
relied_on 必須明確指向 Frank 核心狀態、原則、模型或先前 entry id。
日期格式統一 YYYY-MM-DD
type 只允許 "directional" 或 "external_prediction"，預設 "directional"
產生後自動在 JSON 最後加一個欄位 "generated_by": "AI-assisted"（除非使用者明確說不要）

完整 JSON 格式（嚴格遵守）
{
"id": "j-XXX",                  // 自己遞增或使用者指定
"date": "YYYY-MM-DD",
"type": "directional",
"context": "一句話場景描述",
"judgment": "我的結論",
"relied_on": "依據的原則/模型",
"prediction": "預測內容",
"confidence": "高/短期",
"verification_condition": "可驗證證據（external_prediction 必填）",
"predicted_timeline": "時間範圍（external_prediction 必填）",
"captain_actual_response": "實際結果或對方回應",
"captain_review": "校準判斷",
"captain_correction": "修正內容",
"success_factors": "成功條件與模式（只在正確時填）",
"generated_by": "AI-assisted"   // 自動加這行
}
```

建議存放方式：

單一資料夾放所有 .json（按年份或 id 排序）
或用 Notion / Obsidian 每筆一頁 + JSON 區塊

欄位詳細說明
```
欄位,說明,必填條件
id,唯一識別（j-001 起）,必填
date,記錄日期,必填
type,directional（預設）或 external_prediction,選填（舊 entry 預設 directional）
context,一句話描述場景,必填
judgment,自己的結論,必填
relied_on,依賴的原則/模型/推導,必填
prediction,directional: 預測對方看法；external: 預測事件,必填
confidence,信心標記（可分方向/時間，如 高/短期）,必填
verification_condition,什麼具體證據可驗證（external 專用）,external 必填
predicted_timeline,預期時間範圍,external 必填
captain_actual_response,實際發生 / 對方真實回應 / 驗證資料,必填（驗證後）
captain_review,校準判斷（通常由「使用者」或外部視角填）,驗證後填
captain_correction,具體校準內容,驗證後填
success_factors,成功條件與可提取模式（只在正確時填）,選填
```

欄位說明必填條件id唯一識別（j-001 起）必填date記錄日期必填typedirectional（預設）或 external_prediction選填（舊 entry 預設 directional）context一句話描述場景必填judgment自己的結論必填relied_on依賴的原則/模型/推導必填predictiondirectional: 預測對方看法；external: 預測事件必填confidence信心標記（可分方向/時間，如 高/短期）必填verification_condition什麼具體證據可驗證（external 專用）external 必填predicted_timeline預期時間範圍external 必填captain_actual_response實際發生 / 對方真實回應 / 驗證資料必填（驗證後）captain_review校準判斷（通常由「團長」或外部視角填）驗證後填captain_correction具體校準內容驗證後填success_factors成功條件與可提取模式（只在正確時填）選填
目前狀態

v0.1：方向性預測使用較多，external_prediction 正在測試
未來可能迭代：加時間戳、影響權重、相關 entry 連結

LICENSE
MIT License – 歡迎 fork、改、商用，但保留原作者署名。
有任何欄位調整建議或使用心得，歡迎開 issue。
