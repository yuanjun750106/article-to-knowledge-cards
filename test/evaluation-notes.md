# Forward-test evaluation notes

Internal development record. Each test states the source, the knowledge units actually present, and the failure modes being probed.

## Results, round 1 (SKILL.md v1)

Each test was run by an independent agent that was given only the skill and the source, and no hint about what to look for.

| Test | Result | Verdict |
| --- | --- | --- |
| T1 remote work | 7 cards, all traceable, no invented numbers, no duplicates | Pass, with one defect: fields were transcribed from the source rather than condensed (card 3's 核心知识 ran to a long paragraph) |
| T2 GBK file | 8 cards, all traceable; decoded the GBK file on the first attempt without reporting an encoding error; explicitly noted it dropped the timezone caveat because it hit the 8-card ceiling | Pass on encoding. This is also why the deck was not the 7-unit core: filling all 8 slots pushed out a genuine caveat |
| T3 coffee (thin) | 5 cards from a 4-unit source | **Fail**: reached the 5-card minimum by splitting unit 1 ("oxidation is the main cause; temperature only sets the rate") into two cards, the second of which only restated the first |
| T4 boundary questions | Correct on all five behaviours | Pass as behaviour; surfaced 8 ambiguities listed below |

### Defects found and fixed in SKILL.md v2

1. No stated lower bound for a thin source, so "3 or 4 cards is fine" was not enough to stop padding. v2 makes the second pass explicitly a hunt for *missed* units, "never to split an existing unit in two".
2. "One card, one point" gave no way to test whether a draft was atomic. v2 adds the merge test and the requirement that a 简明解释 must say something the 核心知识 does not.
3. Fields were transcribed instead of condensed. v2 adds sentence budgets (核心知识 1-2, 简明解释 1-3) and a "Condense; do not transcribe" rule.
4. The `补充背景` escape hatch directly undermined "no fabrication". Removed in v2: if a point cannot be explained from the source, drop the card.
5. Hardcoded Chinese field labels collided with "write in the source's language". v2 states the rule: labels stay fixed for scannability, content follows the source language, punctuation follows the source language.
6. The short-deck reason had no slot in the output template. v2 adds a trailing 说明 line.
7. "usually drop out" left background material card-eligible. v2 says leave it out.
8. Mixed themes left the proceed-or-ask branch open. v2 requires choosing the central theme, declaring the scope, and mentioning what was left out.

### Round-2 convergence retest

The three generation tests were re-run against SKILL.md v2 with the same sources, to confirm the fixes (especially the thin-source floor and field length) without regressing the passing behaviour.

| Test | Source | Encoding / form | Probes |
| --- | --- | --- | --- |
| T1 | `article-remote-work.md` (argumentative, ~11 units) | UTF-8 MD file | selection quality, non-duplication, atomicity, no fabricated numbers |
| T2 | `article-log-archive-gbk.txt` (procedure, ~8 units) | GBK TXT file, no BOM | encoding fallback, command/parameter precision, caveat retention |
| T3 | `article-coffee-thin.md` (thin, 3 units) | UTF-8 MD file | anti-padding: must not reach 5 |
| T4 | prompt-only | — | boundary behavior (URL, thin input, "add what you know") |

## Ground truth: units actually supported by each source

### T1 remote work

1. Remote work does not by itself raise efficiency; what matters is how interruptions are distributed.
2. Office interruptions are visible and tend to cluster in one part of the day.
3. Remote work scatters interruptions into messages/notifications; each looks cheaper but total count rises, fragmenting the whole day.
4. Recovery cost from an interruption is several times the interruption's own duration; 10+ minutes is common for multi-thread tasks.
5. Judging impact requires where interruptions land and their spacing, not just how many occurred.
6. Reducing total interruption count gains less than concentrating interruptions.
7. Same 5 interruptions spread over 5 hours can ruin an afternoon; clustered in 30 minutes they preserve deep work.
8. Therefore define protected no-interruption periods rather than pursuing zero interruptions, which is nearly unachievable in collaborative settings.
9. Under a "respond anytime" default, interruption becomes an obligation and askers stop judging urgency.
10. Writing response-time expectations into team agreements changes the asker's urgency judgement, not the tool.
11. Tools only lower the visible cost of interruption, not recovery cost; switching tools treats a structural problem as a tool problem.

Expected: 5–8 cards, no invented statistics. Any specific number beyond "several times", "10+ minutes", "5 interruptions / 5 hours / 30 minutes", or "7 days" is fabricated.

### T2 log archive

1. Rotate/confirm the current day's file is no longer being written before archiving, else the archive is incomplete.
2. `-mtime +7` selects by modification time, not by the date in the filename.
3. gzip replaces the original with a `.gz` file.
4. Bucket archives by month using the file's modification time, not the execution time — otherwise a batch run at month end dumps everything into one month directory.
5. Preview a delete condition with `-print` before replacing it with `-delete`; deletion is the only irreversible step.
6. The flow assumes already-rotated logs; single-file logging must be handled first or compression fails.
7. `-mtime` is affected by timezone.
8. gunzip makes archiving reversible.

Fabrication traps: "keeps only 7 days" is stated as a goal (retain 7 days of raw files), 500MB threshold, 365-day deletion, 30-day archive age, 5-minute modification check. Any other threshold is invented.

### T3 coffee (thin source)

Units 1 and 2 below are one knowledge unit, not two: "temperature only sets the rate" is the reason freezing works at all, which is exactly the merge test the skill now applies. Treating them as separate cards is the padding failure this test exists to catch.

1. Flavour loss in roasted beans comes mainly from oxidation on contact with air; temperature only changes the rate — and freezing slows that loss precisely because it is a low temperature. (one unit)
2. What decides whether freezing helps is portioning, not freezing itself; repeated take-out/thaw/refreeze degrades flavour fast because each removal exposes beans to air and moisture.
3. After portioning, little warming time is needed before grinding.
4. The advice applies only to roasted beans; green beans are stored differently (explicitly out of scope).

Expected: 4 cards. Reaching 6–8 is a failure, and 5 is a warning sign that the merge test was not applied.

## Round-2 results (SKILL.md v2)

| Test | Result | Verdict |
| --- | --- | --- |
| T1 remote work | 7 cards, no fabricated content, fields now condensed rather than transcribed | Pass |
| T2 log archive (GBK) | not re-run; the load step was unchanged by the v2 edits, and round 1 already decoded the GBK file correctly | Covered by round 1 |
| T3 coffee (thin) | 5 cards | **Failed again**: the model wrote "the source supports 5 units" and split the oxidation rule from its freezing consequence. The v2 merge rule was not operational enough — the pair passed the "same sentence" wording because the source puts the two statements in separate paragraphs |
| T4 English source | 5 cards, all fields in English with the Chinese labels preserved, punctuation half-width | Pass: the language rule produces a predictable bilingual shape |
| T4 boundary questions | all five behaviours correct | Pass |

### Round-3 retest after making the merge test operational

The failure above is why constraint 5 now spells out the rule-plus-consequence case and states the merge test as a pairwise check that can actually be run. Re-running T3 against that version:

| Test | Result | Verdict |
| --- | --- | --- |
| T3 coffee (thin) | **4 cards**, with a trailing `说明：` line naming which two pairs were merged and why | Pass: the padding failure is fixed |

The agent's own reasoning named both merges explicitly ("冷冻能放慢风味流失" is a direct consequence of "氧化致流失、温度只影响速度"; "反复解冻使风味快速下降" is a consequence of "分装决定效果"). That is the intended behaviour rather than a lucky short deck.

## Round-3 audit findings and fixes

A separate agent audited the finished `SKILL.md` and `references/example.md` for internal contradictions and unenforceable rules. Two defects were blocking:

1. **Field count.** The body said "A card is five fields" while listing four. Fixed to "four".
2. **The 3-unit path was contradictory.** The selection step said "Select 5-8 units" while the constraints said 3 or 4 is fine, and the self-check still read "between 5 and 8". A literal reading padded a 3-unit source; a careful one produced a deck that failed the self-check. The selection step now asks for 5-8 *when the source supports it*, explicitly allows 3-4, and the self-check band is 3-8 with any shortfall below 5 explained.

The same audit produced these should-fix items, all applied:

- "Never split one point across cards" (rule 2) read as contradicting "split a compound draft" (rule 3). The two kinds of splitting are now distinguished in rule 3.
- The self-made-example rule required a label but named no token; it is now `自拟例子：`.
- The fragment-stop branch never said what the reply contains; it now specifies a one-or-two-line refusal and ask.
- "Narrow the scope" had no criterion or order; it now says to narrow before mining and by which principle.
- The fallback in Boundaries assumed text was available; a request with nothing but a link or a bare export request now has a defined answer.
- `.markdown` was accepted in step 1 but missing from the frontmatter and Boundaries list.
- The calibration deck itself broke two rules it exists to teach: 卡片 1's 简明解释 restated its 核心知识 (the exact failure rule 5 forbids), and 卡片 3's 简明解释 transcribed a source sentence. Both were rewritten. 卡片 4's last field carried both an example and a question. 卡片 1's last field used a made-up "review ten times" illustration with an invented number, which the no-fabrication rule forbids; it was replaced with a question the source answers.
- The example's commentary section is now an HTML comment so a reader cannot mistake it for part of the deck, and it includes a labelled `自拟例子：` example, the one behaviour the rules regulate but the deck never showed.

## Pass criteria

- Card count within 5–8, or a stated reason for fewer.
- Every claim traceable to the source; zero added facts, numbers, or external best practices.
- One point per card; no two cards sharing the same underlying unit.
- 简明解释 adds substance instead of restating 标题/核心知识.
- Self-test questions answerable from the source alone.
- File inputs read successfully regardless of UTF-8/GBK encoding.
