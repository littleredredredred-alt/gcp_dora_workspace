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

## Core workflow

1. Capture first.
2. Do not reject input because it is messy.
3. Preserve the original wording when it may matter later.
4. Apply light structure.
5. Store to the right file.
6. When uncertain, store to inbox instead of blocking.

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
