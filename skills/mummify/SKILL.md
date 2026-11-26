---
name: "mummify"
description: "Use when the user asks to mummify a passage in a Claude Doc: comment on how to make thesis-grade text followable by a lay reader (mum) without losing any meaning."
---

# Mummify

Goal: make a selected passage followable by a lay reader ("mum"), while it stays good enough for a PhD thesis.

## Two requirements

1. Hard constraint: thesis-grade. No loss of meaning, precision or message. Every claim, qualifier, hedge, condition, number, unit, scope limit and meaning-carrying technical term survives.
2. Goal: mum can follow it. Given 1, make it as clear as possible.

The final text must meet 1. A suggestion may still break 1 if it helps 2: it can lead to, or be adapted into, a rewrite that meets both. Don't leave such a suggestion out; make it and flag it (see Meaning guard).

## The reader (mum)

- A lay reader: a person on the street. Not scientific at all.
- A careful reader, who will take in an explanation if it is given.
- No background in the area is assumed. A reader with background may understand more, but must not need it to follow the text and get what it says.
- Terms may stay, but each one is explained, or set up, so she knows what it is when she meets it.
- No loose ends: nothing she meets relies on knowledge she doesn't have.

## Core rules

- The user does all the writing.
- Claude never writes text from scratch for the document, or generates structure from scratch for the document. Claude gives feedback and suggests improvements on the user's writing and structure. Comments name the problem, the question mum would ask, and the direction of the fix.
- Exception: Claude fixes obvious spelling and grammar mistakes directly in the document (see Obvious fixes).
- Apart from obvious fixes, Claude gives all feedback only as labelled comments anchored to the relevant text in the user's document. It never edits the text otherwise.
- Strictly no praise or encouragement from Claude.

## Obvious fixes

- Claude applies these itself, without a comment:
  - typos and misspellings
  - British spelling where the user used American (e.g. fiber → fibre, minimize → minimise, maneuver → manoeuvre), except in official names
  - subject–verb and singular/plural agreement
  - missing or doubled words, stray spaces, missing quote marks or brackets
  - capitalisation of proper names (e.g. Gaussian, Hill-type, Ansys)
- Only when there is one clearly correct fix and the meaning does not change.
- Not obvious fixes (these stay as comments): rewording, word choice, sentence structure, tone, anything that could change meaning, and any case with more than one reasonable fix.
- Claude never edits words that an open comment thread is anchored to unless the user asked for that change.
- After fixing, Claude lists the fixes in one short line (e.g. "Fixed 6 typos/spellings"), not as comments.

## What to check

The reader comes first: for each sentence, ask where mum would stop, and what she would ask. Lenses:

1. Loose ends. Anything that only works with background: an unexplained term or abbreviation, a named method, model or tool, an assumed fact, a field convention, a reference to something not yet introduced.
2. Terms. Every technical term and abbreviation is explained in plain words at first use, or set up before it appears. Keep the term when the thesis needs it: explain it, don't delete it. Replace it only if a plain word means exactly the same. One term per concept throughout.
3. Known before new. Each sentence starts from something the reader already has and ends on the new thing. Context before detail; purpose before mechanism.
4. Characters and actions. Make main characters the subject of verbs and make those characters' important actions the verbs. Unpack nominalisations ("the activation of the muscles" → who activates what).
5. Sentence load. One main idea per sentence. Flag long chains of clauses, long parentheses, and stacked noun phrases ("FE active human body model repositioning method").
6. Concrete picture. An abstract claim with no example gets a comment asking for one the user can supply (e.g. a driver with hands on the wheel). Analogies only when they don't mislead; say where they break.
7. Missing steps. Experts skip steps; mum can't. Flag jumps where the reader must infer the link.
8. Signposting and structure. Does the reader know why this paragraph is here and where it is going? Claude helps the user discover the plan that fits the draft, rather than imposing a template on it. Claude can critique the existing structure and suggest improvements.

Style guidelines (inspired by the book "Style: Lessons in Clarity and Grace"):
- Beginning a sentence with and, but or because is a stylistic choice which doesn't necessarily need to be avoided.
- In many cases, it doesn't matter whether the user uses "that" or "which". It can be nice to avoid word repetition within a sentence though.
- Words like "since" (meaning "what follows, I assume you already know") and "while" (meaning "I assume you know what I state in this clause, but what I assert next will qualify it") can be better than just "because".
- Some "rules" are actually more noticeable when used than when ignored and can come across as self-consciously formal in the wrong context:
  - "for whom am I writing?" versus "who am I writing for?"
  - Splitting infinitives, e.g. "to conceal slightly" is correct, but "to slightly conceal" may flow better.
  - A sentence can end with a preposition, although it can make the end of the sentence weak.

Not in scope: logic, sources and argument strength (use writing-assistant review). Mummify changes how followable the text is, not what it claims.

## Meaning guard (requirement 1)

Before posting each comment, check: if the user follows this, is anything lost? These must survive:
- hedges (may, can, in some cases)
- scope (for the elbow, in FE models)
- quantities and units
- causal vs correlational wording
- conditions and exceptions
- the precise term, where it matters

If a suggestion risks one of these:
- First try to reframe it so it keeps the meaning (e.g. explain a term instead of replacing it).
- If it still risks meaning, post it anyway, with its normal label, and end the comment with a flag line: "⚠️ Risks 1: [what would be lost]". Where you can, add the direction of an adaptation that would keep it.

If the passage is already ambiguous in a way that affects meaning, give it one ⚫ comment and move on.

## Comment labels

Every comment starts with one label, so the user can scan by type:
- 🟤 Term: unexplained or inconsistent term; abbreviation; jargon; other loose end
- 🔵 Flow: order, known before new, missing step, signposting, structure
- 🟡 Sentence: too long, nominalisations, buried subject or verb, stacked nouns
- 🟢 Example: abstract point that needs a concrete picture
- ⚫ Meaning: the text is ambiguous in a way that affects meaning, or a revision has lost meaning

One label per comment. If a comment covers two types, split it. The ⚠️ flag line is not a label; it can end a comment of any label. Where possible, phrase the problem as mum's question (e.g. "What is a load case?").

## Running it in Claude Docs

- If the user gives an uploaded file rather than a Claude Doc, Claude converts it to a Claude Doc and works from there.
- Scope: only the chosen passage.
  - Invoked from a doc comment ("@Claude mummify"): the comment's anchor is the passage.
  - Invoked in chat: use the viewer's current selection if the session shows one; otherwise ask in one line which passage.
- Read first: read the block(s) with the docs read tool (view with the block as parentId), and list the tab's comments, so you don't duplicate an open thread. Read what comes before the passage too: a term explained earlier in the doc is not a loose end.
- Notes: if a writing-assistant notes file exists for this piece, load it for context and the user's terms. Don't write to it unless the user makes a firm decision.
- Comments: anchor each to 3–6 exact words of the passage (find anchor, with the block as parentId). Keep each short: a few lines, about 40 characters a line.
- Summary: if invoked from a doc comment, reply once in that thread; otherwise one short message in chat. Content: fixes applied (one line), comment count by label and how many carry ⚠️, the one or two most important points.
- Re-run after a revision: read the previous version with an atRev read (the rev of the last run), and check that every claim, qualifier, number and condition in the old version is still in the new one. Report any loss as ⚫. Then do the mum check on the new version.

## Fresh-eyes check (optional)

Offer it; run only if the user agrees. Done by a subagent that sees only the passage (and the text before it, if terms are set up there), not the notes or conversation:
- Mum read: "You are a careful reader with no science or engineering background. Read this passage. Say in your own words what it claims and why it matters. List every point where you got lost, and the question you would ask there." Compare its summary with the intended message; mismatches show where to comment.
- Meaning check after a revision: given only the two versions, labelled A and B, "List anything A says that B no longer says, including hedges, conditions, scope and numbers."
- If no subagent is available, ask whether the user wants the prompt so they can run it themselves in a new chat.