---
name: second-brain
description: Low-friction personal memory system for a second-brain assistant. Use when the agent should accept arbitrary user input from chat (Telegram now, Discord later), store it without friction, organize it into durable notes, and answer future recall questions from those notes. Triggers include: save this, remember this, note this, keep this link, store this idea, what did I say before, what do you know about X, what was that link, what project/person/idea did I mention, inbox processing, personal notebook workflows, and capture/retrieval design.
---

# Second Brain

Dora acts like Eric's portable notebook.

Primary promise:
- accept almost any input
- preserve it durably
- organize just enough for future retrieval
- answer later questions from stored notes

## Schema Definition（Phase 2 結構化記憶）

### Memory Files

| 檔案 | 用途 | 寫入時機 |
|------|------|---------|
| `memory/INBOX.md` | 模糊/片段/未分類的 capture | 快速 capture，不確定分類時 |
| `memory/PEOPLE.md` | 人物相關筆記 | 提到人名、關係、互動時 |
| `memory/PROJECTS.md` | 專案相關筆記 | 提到專案、任務、進度時 |
| `memory/LINKS.md` | 連結收藏 | 收到值得保存的 URL 時 |

### Entry Type System

| type | 格式 | 使用時機 |
|------|------|---------|
| `idea` | 想法、點子、創意 | 任何非立即執行的念頭 |
| `link` | 標題 + URL + why_it_matters | 收到 URL 或想收藏連結 |
| `note` | 一般筆記、觀察、記錄 | 不符合其他類型時 |
| `task` | 行動項目、相關的人 | 有明確後續要做的事 |
| `question` | 想問的問題、以後要研究 | 想到但還沒找到答案 |

### Entry Pattern（每筆記錄必備欄位）

```markdown
- YYYY-MM-DD HH:MM UTC | type: {type} | source: {telegram|discord|webchat}
  summary: {一句話描述}
  details: {詳細內容 or N/A}
  related: {comma-separated tags}
```

### Linking Rules

1. **同一專案/人物的多筆記錄**：透過 `related` 標籤關聯
2. **跨檔案連結**：在 `related` 使用相同 tag
3. **定期整理**：當 `related` 累積超過 3 個相同 tag 時，考慮濃縮

```markdown
# Example: LINKS.md 中的連結可以 cross-reference
- 2026-04-07 16:00 UTC | type: link | source: telegram
  title: 關於 RAG 的研究文章
  url: https://example.com/rag-research
  why_it_matters: 之後要做 Dora 的 semantic search
  related: dora, rag, semantic-search
```

### Recall Rules（查回流程）

1. **關鍵字搜尋**：直接 grep memory/ 檔案
2. **多檔案搜尋**：INBOX → PEOPLE → PROJECTS → LINKS
3. **交叉比對**：當多個檔案有相關內容時，顯示所有結果

```markdown
# 查回時的回答格式
直接回答：
{summarized answer}

依據：
- {file1}: {relevant excerpt}
- {file2}: {relevant excerpt}

無法確定時：
「我找到一些相關記錄，但不確定是否完全符合：
{list}
建議再提供更多細節。」
```

### Query Pattern Recognition

| 使用者問法 | 對應行動 |
|-----------|---------|
| `記一下...` / `save this` | capture → INBOX.md |
| `我之前有没有提到...` | recall → 搜尋所有 memory |
| `誰說過...` / `記得...說過` | recall → PEOPLE.md |
| `那個專案...` / `project...` | recall → PROJECTS.md |
| `整理 inbox` | cleanup → 濃縮/分類 INBOX.md |

### Phase 2 Enhancements（已實作）

- 所有 entry 統一 timestamp format（YYYY-MM-DD HH:MM UTC）
- `related` 標籤系統用於 cross-file linking
- source channel 記錄用於日後分析
- 每個檔案有明確職責邊界

## Supported user intents

Handle these kinds of inputs as first-class workflows:
- random thought
- idea stub
- useful link
- meeting/detail to remember
- person context
- project context
- reminder-like note for later reference
- follow-up query asking what was stored before

## Storage model

### 1. Inbox
Use `memory/INBOX.md` for raw or weakly-structured captures.

Use when:
- the content is vague
- multiple categories may apply
- the user is sending quick fragments
- the note should be saved immediately with minimal interpretation

### 2. People
Use `memory/PEOPLE.md` for person-centric notes.

Store:
- who they are
- relationship/context
- what they said / recommended / requested
- why it matters
- latest relevant interaction note

### 3. Projects
Use `memory/PROJECTS.md` for project-centric notes.

Store:
- project name
- idea / status / next step / decision
- important links and references
- blockers and open questions

### 4. Tasks
If the user clearly wants execution tracking rather than memory, use task files instead of second-brain memory files.

## Entry format

Prefer compact structured blocks like:

```markdown
- 2026-03-30 17:20 UTC | type: idea | source: telegram
  summary: 想做一個可以隨時記錄與查回的 Dora 第二大腦
  details: 核心是低摩擦 capture，之後要能從 Telegram / Discord 隨意丟內容進來
  related: dora, second-brain, recall
```

For links:

```markdown
- 2026-03-30 17:20 UTC | type: link | source: telegram
  title: Interesting article about hybrid search
  url: https://example.com/hybrid-search
  why_it_matters: Dora 之後要做 keyword + semantic search
  related: search, hybrid-search
```

For people:

```markdown
## John Doe
- latest_updated: 2026-03-30 17:20 UTC
- context: 做 OCR startup 的朋友
- notes:
  - 推薦一篇 document AI 文章
  - 之後可以再問他標註流程
```
```

For projects:

```markdown
## Dora Second Brain
- latest_updated: 2026-03-30 17:20 UTC
- status: active
- summary: Eric 的隨身筆記本 agent
- decisions:
  - Telegram 先上線
  - Discord 之後再加 bot
- next_steps:
  - 做穩定 capture 規則
  - 之後做 dashboard / hybrid search
```
```

## Capture rules

- Keep captures short and append-only by default.
- Do not rewrite old entries unless doing explicit cleanup.
- Save links with context, not just URL.
- Save people with relationship context.
- Save projects with status and next step.
- Save vague notes to inbox rather than forcing classification.
- If the user says "記一下 / 幫我記住 / save this / remember this", always persist it.

## Retrieval rules

When the user asks a recall question:

1. Search relevant memory files.
2. Prefer exact answer first.
3. Add short supporting bullets.
4. Mention uncertainty when evidence is weak.
5. If multiple candidate memories exist, show the top few.

Search order:
- `memory/INBOX.md`
- `memory/PEOPLE.md`
- `memory/PROJECTS.md`
- task files only when the query is action-oriented

## How to answer recall queries

Use this pattern:

1. direct answer
2. 1-3 supporting bullets
3. source file references when helpful

Example:

```text
有，你之前提過 Dora 的核心是「隨意丟內容進來，之後都找得回來」。
- Telegram 先上線
- Discord 之後再補 bot
- 後續會做 dashboard + hybrid search
Source: memory/PROJECTS.md
```

## Cleanup / consolidation

When the user asks to整理/清 inbox:
- merge duplicated fragments when clearly the same topic
- move mature person notes into `PEOPLE.md`
- move mature project notes into `PROJECTS.md`
- keep important original wording if nuance matters

## Product evolution path

### V1
- markdown-based capture
- inbox / people / projects split
- durable note recall by file scan

### V2
- metadata normalization
- summaries
- related-note linking
- stronger people/project pages

### V3
- dashboard
- keyword + semantic hybrid search
- metadata filtering
- timeline / related notes / review flows

## Guardrails

- Do not promise perfect memory if the note was never saved.
- Do not fabricate prior notes.
- Do not silently drop user content.
- When a note may contain secrets, preserve carefully and avoid unnecessary duplication.
