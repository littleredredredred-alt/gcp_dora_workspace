# Dora `second-brain` Skill Spec

## Goal
把 Dora 做成 Eric 的隨身筆記本助理：
- **隨手收**：Telegram 現在可直接丟任何內容；Discord 未來補 bot 後同樣適用
- **記得住**：內容會被落到 Dora workspace 的 memory 檔案
- **找得到**：之後再問 Dora，可以從已存內容中查回答案

---

## Primary UX

### Capture
Eric 可以直接傳任何內容給 Dora，例如：
- 一句想法
- 一個網址
- 一段 meeting note
- 一個人名與背景
- 一個專案的下一步
- 一段之後想查回的零碎資訊

Dora 的原則是：**先收，不擋。**

### Recall
之後 Eric 可以直接問：
- 我之前有沒有提過某件事？
- 那個連結是什麼？
- 我上次怎麼描述這個想法？
- 你記得某個人/專案嗎？

Dora 會從記憶檔案裡查回相關內容並回答。

---

## Current Storage Design

### `memory/INBOX.md`
放原始、快速、未完全整理的 capture。

### `memory/PEOPLE.md`
放人物相關的長期筆記。

### `memory/PROJECTS.md`
放專案相關的長期筆記與決策。

### Task files
若內容是「要執行 / 要追蹤」而不是「要記住」，應進入 task system，而不是 second-brain memory。

---

## Entry Types

目前 skill 支援以下概念型別：
- `idea`
- `link`
- `person`
- `project`
- `task`
- `journal`
- `reference`

如果 Dora 無法立即準確分類，**先寫進 inbox**，不要丟失內容。

---

## Retrieval Strategy

目前是 **V1 file-based retrieval**：
1. 掃描 `INBOX.md`
2. 掃描 `PEOPLE.md`
3. 掃描 `PROJECTS.md`
4. 必要時補看 task files

回答策略：
1. 先給直接答案
2. 再給 1-3 個 supporting bullets
3. 必要時附 source file

---

## Product Roadmap

### V1（已落地）
- Telegram capture
- markdown durability
- inbox / people / projects 分流
- 基本 recall pattern

### V2
- metadata normalization
- note linking
- summary 強化
- inbox cleanup workflow

### V3
- Next.js dashboard
- keyword + semantic hybrid search
- metadata filters
- related notes / timeline

---

## Trigger Examples

### Capture 類
- 幫我記一下這個想法
- 記住這個網址
- 這個人是做 OCR 的，之後可能會合作
- 我想到一個新專案方向

### Recall 類
- 我之前有提過這件事嗎？
- 那個連結是什麼？
- 你記得某某人嗎？
- 我上次怎麼講這個專案？

---

## Guardrails
- 不可以假裝記得沒存過的內容
- 不可以因為分類不清楚就拒絕保存
- 對敏感資訊要保守，不做不必要複製
- recall 時若證據不足，要明講不確定
