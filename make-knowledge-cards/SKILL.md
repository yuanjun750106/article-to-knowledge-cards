---
name: make-knowledge-cards
description: Turn a pasted article or a local Markdown/TXT file into 5-8 knowledge cards, each teaching exactly one important point with a plain explanation and an example or self-test question. Use when someone wants to study, review, or share the key knowledge from a long text. Handles pasted text and local .md/.txt files only; no web scraping, PDF, Anki export, or GUI.
license: MIT
metadata:
  short-description: Turn an article into knowledge cards
---

# Make Knowledge Cards

Convert one source text into a small set of cards that let someone relearn the material later without rereading the source.

A card is four fields:

| Field | Meaning |
| --- | --- |
| 标题 | The single point of this card, stated as a claim |
| 核心知识 | The knowledge itself, 1-2 sentences, precise and complete on its own |
| 简明解释 | Why it is true or how it works, 1-3 sentences in plain language |
| 例子或自测问题 | One concrete illustration, or one question answerable from the source |

## Hard constraints

These five rules decide whether the output is acceptable. Everything else is judgement.

1. **No fabrication.** Every claim on a card must come from the source. Never add facts, numbers, names, dates, steps, examples, or conclusions from your own knowledge, and never "correct" the source. You may rephrase, condense, reorder, and simplify only what the source supports. When a point cannot be explained without outside knowledge, drop the card — do not import the missing knowledge.
2. **No padding.** If the source genuinely supports only 3 or 4 cards, deliver 3 or 4 and say why in the trailing `说明：` line. Fabricated or filler cards are worse than a short deck. Never split one point across cards, or inflate a minor detail, to reach the count.
3. **One card, one point.** A knowledge unit is one claim a reader could recall, apply, or be tested on by itself. A phrase, a lead-in, or half of another claim is not a unit. If a single draft card tries to carry two independent claims, split it into two cards; if one of them is not worth a card, drop it. Splitting a compound draft is not the "splitting" rule 2 forbids — rule 2 forbids cutting one point into two cards.
4. **No duplicates.** If two cards would be answered by the same sentence, keep the stronger one. Repetition in the source is not importance — it is one knowledge unit.
5. **Merge rather than split.** A source that states a rule and then a specific consequence of that rule has given you one unit, not two: "temperature only sets the rate" and "freezing therefore slows the loss" are the same knowledge tested twice. Attach the consequence to the rule as its 简明解释, or drop it. Run this merge test over every pair before finalising the count: if either card's self-test question could be answered by the other card's 核心知识, the pair is one card.

## Workflow

### 1. Load and scope the source

- Pasted text: work from it directly.
- Local file (`.md`, `.markdown`, `.txt`): read the whole file before deciding anything. If it is not valid UTF-8, retry with the local default encoding (for example GBK on Windows) rather than reporting failure.
- Note the title, the apparent purpose, and the audience. For pasted text, use its own heading as the title when it has one; otherwise derive a short descriptive title from the content. If several unrelated themes appear, choose the most central one, state that scope in the `覆盖范围` line, and mention that the other themes were left out so the user can ask for a second deck. Never silently mix unrelated themes in one deck.
- If the source is a fragment that does not support even 3 independent cards, reply in one or two lines: say the text is too short for a meaningful deck, name what little is in it, and ask for more text. Do not emit a one- or two-card deck under the deck heading and do not produce hollow cards.
- When the user provides both a fragment and a larger file, prefer the larger source.

### 2. Mine knowledge units

Read the source and list every candidate knowledge unit: one unit is one fact, definition, distinction, cause, mechanism, rule, or procedure step that a reader could be expected to recall, apply, or be tested on. List them before writing cards; this list is what you select from.

### 3. Rank and select

Rank the candidates by how much each one matters to this source:

- What is the source fundamentally about? The central claims outrank supporting detail.
- What is a prerequisite for understanding the rest?
- What is counterintuitive, or differs from the common assumption?
- What would change a reader's decision or action?
- What is stated as a rule, distinction, or conclusion, rather than a passing illustration?
- What is only background, given for completeness, or already common knowledge? (Leave these out.)

Select the units that stand alone — cards should not require each other to make sense, because they will be read out of order. The target is 5-8 cards: deliver 5-8 when the source supports them, deliver the 3 or 4 it genuinely supports when it does not, and narrow the scope when it supports more than 8.

Only when the selected set is below 5 should you revisit the source for a second pass and look for units you missed (distinctions, causes, caveats, conditions). Revisit only for units you genuinely missed, never to split an existing unit in two. If the second pass yields nothing worth a card, deliver the smaller deck and state the shortfall in the trailing 说明 line. A 3- or 4-card deck is a correct result, not a failure to meet a quota.

When a source is large, narrow the scope before mining units: pick the section, theme, or argument that carries most of the source's substance, or the one the user named, and state that scope at the top of the output. Do not compress an entire book into 8 cards.

### 4. Write each card

- **标题**: a short declarative claim, phrased as the question the card answers, e.g. "反馈延迟会让学习者在错误动作上多练几十次". Name the subject; avoid "介绍", "概述", "相关概念" and bare nouns. The 核心知识 must directly answer the claim the title makes rather than shifting to a different level of explanation.
- **核心知识**: what must be remembered — 1-2 sentences. Prefer the source's own terms; define a term in the same breath if the source does.
- **简明解释**: why the 核心知识 is true, or the mechanism behind it — 1-3 sentences. It must add something the 核心知识 does not already say; an explanation that only restates or applies it means the card should be merged with the one it duplicates.
- **例子或自测问题**: one item only. Prefer an example the source itself gives; otherwise ask a question that the source's content answers. Do not pad a card with both its own example and a question. If you construct an example rather than quoting one, label it `自拟例子：` and build it only from facts the source states — never introduce a number, name, or scenario detail the source does not contain. When the source contains no example, prefer the question: the question can be correct while an invented illustration usually cannot.
- Condense; do not transcribe. A card is a study aid, not a quotation. Keep each field to its own sentences rather than copying a long passage out of the source, and cut source details that do not serve the card's single point.

### 5. Language

Keep the field labels and headers shown above as they are, so a deck produced from any language stays scannable and comparable. Write the content of every field — titles included — in the language the source text is written in, and follow that language's punctuation conventions (full-width in Chinese, half-width in English; for other languages or mixed text, use the dominant language of the source and stay consistent). Keep technical terms, commands, and identifiers in their original form when translating them would obscure them. If the user asks for another language, translate the content faithfully to the source.

### 6. Self-check before delivering

Verify each point and fix what fails:

- Trace every card back to specific source content. Drop anything you cannot trace.
- Every card carries knowledge from the source; nothing on it is background, filler, or common knowledge that the source merely assumed.
- 标题 and 核心知识 say the same single thing; no card hides two points. The title names the subject rather than reading "介绍", "概述", or a bare noun.
- No two cards share the same underlying knowledge. Run the merge test over every pair, in both directions: could either card's self-test question be answered by the other's 核心知识? If yes, merge them.
- Each 简明解释 says something the 核心知识 does not already say.
- No field exceeds its sentence budget, and no field transcribes a long passage from the source.
- Each 例子或自测问题 holds one item, is answerable from the source, and is labelled `自拟例子：` if you built it instead of quoting it.
- Each card makes sense alone, without the others.
- The count is 3-8 and any shortfall below 5 is explained in the trailing `说明：` line.

## Output format

Output Markdown, in this shape:

````markdown
# 知识卡片：<来源标题>

来源：<文件名，或"粘贴文本"> · 卡片数：N（覆盖范围：<如是节选或只覆盖某个主题则写明>）
覆盖要点：<用一行列出本组卡片覆盖的知识点，便于快速检索>

---

## 卡片 1：<标题>

**核心知识**：...

**简明解释**：...

**例子或自测问题**：...

---

## 卡片 2：<标题>
...
````

Use the field labels above so cards stay scannable; keep the content in the source's language. Keep one card per `##` heading. Deliver the deck in the reply itself; write it to a file only when the user asks for one.

Close with a single line starting `说明：` when anything needs saying: fewer than 5 cards and why, an unclear or partly unreadable source, or a related point the deck deliberately left out. Omit the line when the deck needs no explanation. Do not add a summary, glossary, or study plan section unless the user asks for it.

## Boundaries

- Input is pasted text, or a local `.md`/`.markdown`/`.txt` file. If asked to scrape a URL, read a PDF, export to Anki, or build a GUI, say plainly that the skill does not cover it, then offer what it can do: make the deck from text the user pastes or from a local text file. If the user supplied no text at all — only a link, or only a request to export — say what is missing, ask for it, and stop there rather than guessing at the contents.
- If asked to name the output format, `.md` keeps the cards editable and copy-pasteable.

Calibration material: [references/example.md](references/example.md) shows a filled card set made from a short sample source, useful when the card shape or level of detail is unclear.
