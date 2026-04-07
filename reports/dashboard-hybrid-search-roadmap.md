# Dora — Dashboard & Hybrid Search Roadmap

> 建立時間：2026-04-07 17:04 UTC
> 用途：Dora second-brain 的 UI 與搜尋能力演進規劃

---

## 現狀（Phase 1，已完成）

- **儲存方式**：純 Markdown 檔案（INBOX.md / PEOPLE.md / PROJECTS.md / LINKS.md）
- **搜尋方式**：關鍵字 grep 全文
- **UI**：無（只有文字檔案）

---

## V1：基礎關鍵字搜尋（Low Priority，短期内不做）

### 目標
- 有一個簡單的 web 或文字介面，可以輸入關鍵字搜尋所有 memory 檔案

### 實作方式
- 用簡單的 HTTP server + grep
- 不需要資料庫

### 限制
- 只支援精確關鍵字
- 無法處理拼字錯誤、同義詞
- 沒有 ranking

---

## V2：Semantic Search + Metadata（Medium Priority，有明確需求再做）

### 目標
- 支援模糊搜尋（`我想找之前看過的...`）
- 可用 metadata filtering（日期範圍、類型、來源）

### 實作方式（預選）

| 方案 | 適合場景 | 缺點 |
|------|---------|------|
| **sqlite-vss** | 本機不需要外部服務 | 需要 pip install + 模型 |
| **bm25**（rank_bm25） | 純關鍵字改善，不需模型 | 不懂語意 |
| **Qdrant / Chromadb（local）** | 有結構化 metadata | 需要另外架服務 |
| **Anthropic / OpenAI API** | 簡單好用 | $ cost + 網路依賴 |

### 推薦：sqlite-vss（本地無需外部依賴）

```python
import sqlite_vss
# 支援 vector search，完全本地，無 API cost
```

### 需要的前置條件
1. 穩定的 entry schema（✅ 已有，T-20260330-002）
2. INBOX 定期清理（避免垃圾進維護）
3. metadata 標籤標準化

---

## V3：Dashboard + Timeline + Review Flows（Long Term，有明確需求再做）

### 目標
- 可視化 timeline（哪天 capture 了什麼）
- 人物/專案/主題的彙總頁面
- 定期複習功能（如每週 inbox review）

### 預選技術

| 功能 | 技術選項 |
|------|---------|
| Dashboard UI | Next.js + Tailwind（簡單快速）|
| 資料查詢 | SQLite 直接讀取 |
| Semantic search | sqlite-vss |
| 部署 | 同 VPS，Node.js app |

### 考量點
- 這階段 Dora 的 workspace 已經有足夠的結構，不需要急著做 UI
- V3 的價值在於「資訊視覺化」與「複習機制」，不是「搜尋能力」
- 優先做 V2 的 semantic search，再做 V3 的 dashboard

---

## 決策點

1. **什麼時候做 V1？** 當 Eric 開始抱怨「要找某筆記很難」時
2. **什麼時候做 V2？** 當 grep 無法滿足recall需求時（例：找不到用過但拼錯的詞）
3. **什麼時候做 V3？** 當需要定期複習而不是被動查詢時

---

## 結論

現在專注做 Phase 1 + Phase 2（schema + recall）是對的。
V2 的 semantic search 可以考慮儘早引入（因為簡單、本地、無額外成本）。
V3 dashboard 是明確的 long-term 目標，但不是短期 priority。

**沒有 blocker，繼續累積 Phase 1 的資料讓 Phase 2 有東西可搜。**