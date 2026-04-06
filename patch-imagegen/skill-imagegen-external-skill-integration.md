# External skill integration instructions for the patched imagegen set

## Objective

Apply a **strictly limited** integration of selected virtues from [skills/image-generation/SKILL.md](../skills/image-generation/SKILL.md) into the patched imagegen documentation set:

- [patch-imagegen/SKILL.patched.md](SKILL.patched.md)
- [patch-imagegen/prompting.patched.md](prompting.patched.md)
- [patch-imagegen/sample-prompts.patched.md](sample-prompts.patched.md)

This document is **not** an open-ended analysis. It is an execution instruction set.

The next agent must apply **exactly** what is specified here and must **not** add extra interpretation, extra edits, or extra “improvements”.

## Why this integration exists

The external skill in [skills/image-generation/SKILL.md](../skills/image-generation/SKILL.md) contains a few reusable strengths:

1. a simple mental model based on subject / context / style
2. better examples of concrete visual specificity
3. stronger guidance for text inside images
4. useful heuristics for character consistency
5. better wording for ambiguous medium selection and factual uncertainty

However, the external skill must **not** be absorbed wholesale because the patched set already has stronger operational discipline around:

- the validated default path through [mcp-image](../src/server/mcpServer.ts:225)
- separation between semantic prompting and runtime execution parameters
- anti-drift rules for edits and iterations
- broader practical scope already preserved in the patched set

## Hard constraints

The implementing agent must obey all of the following:

1. Do **not** redesign the patched architecture.
2. Do **not** rewrite files from scratch.
3. Do **not** add new sections or new headings unless the diff below explicitly says so.
4. Do **not** change any table of contents in this task.
5. Do **not** convert labeled prompt scaffolds into prose paragraphs.
6. Do **not** import the rule “positive descriptions only”.
7. Do **not** reduce the patched scope for UI mockups, wireframes, diagrams, edits, or other already-preserved practical cases.
8. Do **not** introduce runtime attributes such as `quality`, `useWorldKnowledge`, or `useGoogleSearch` into prompt scaffolds.
9. Do **not** modify [patch-imagegen/sample-prompts.patched.md](sample-prompts.patched.md) in this task.

## Implementation order

Apply the work in this order only:

1. update [patch-imagegen/SKILL.patched.md](SKILL.patched.md)
2. update [patch-imagegen/prompting.patched.md](prompting.patched.md)
3. leave [patch-imagegen/sample-prompts.patched.md](sample-prompts.patched.md) unchanged

## Exact required patch for [patch-imagegen/SKILL.patched.md](SKILL.patched.md)

### Reason

This file must receive only a **small policy reinforcement**. The goal is to capture the external skill’s good ambiguity handling without bloating the main skill file.

### Required diff

```diff
*** Begin Patch
*** Update File: patch-imagegen/SKILL.patched.md
@@
 ### Prompt augmentation policy
 - Add only the details needed to improve the output materially.
+- If the requested visual medium is ambiguous, clarify it before adding medium-specific detail.
 - Do not invent extra props, characters, brand elements, side-specific placement, or story details unless implied.
 - For prompts containing exact text, require verbatim rendering.
 - For edits, repeat invariants during each iteration to reduce drift.
*** End Patch
```

### Mandatory interpretation rules for this diff

- The new bullet must be inserted exactly in the `### Prompt augmentation policy` block.
- No other part of [patch-imagegen/SKILL.patched.md](SKILL.patched.md) may be changed in this task.
- The goal is only to force clarification when the user’s intended medium is unclear.

## Exact required patch for [patch-imagegen/prompting.patched.md](prompting.patched.md)

### Reason

This file is the correct destination for most of the value extracted from [skills/image-generation/SKILL.md](../skills/image-generation/SKILL.md). The integration must strengthen prompting quality **without** adding new headings, without changing the TOC, and without weakening the patched set’s existing structure.

### Required diff

```diff
*** Begin Patch
*** Update File: patch-imagegen/prompting.patched.md
@@
 ## Structure
 - Use a consistent order: scene/backdrop -> subject -> key details -> constraints -> output intent.
 - Include intended use (ad, UI mock, infographic) to set the level of polish.
 - For complex requests, use short labeled lines instead of one long paragraph.
+- As a light mental model, make sure the prompt covers subject, context, and style/medium even when using labeled lines.
 
 ## Specificity policy
 - If the user prompt is already specific and detailed, normalize it into a clean spec without adding creative requirements.
 - If the prompt is generic, you may add tasteful detail when it materially improves the output.
 - Treat examples in `sample-prompts.md` as fully-authored recipes, not as the default amount of augmentation to add to every request.
 - For style-sensitive tasks, preserve the requested medium exactly.
+- If it is unclear whether the user wants a photo, illustration, UI mockup, wireframe, or stylized render, clarify before adding medium-specific language.
+- When adding helpful detail, prefer concrete visual categories such as lighting direction/quality/color temperature, materials/textures, atmosphere/air, scale/proportion, and photographic lens/aperture/angle when relevant.
@@
 ## Composition and layout
 - Specify framing and viewpoint (close-up, wide, top-down) and placement only when it materially helps.
 - Call out negative space if the asset clearly needs room for UI or copy.
 - Avoid making left/right layout decisions unless the user or surrounding layout supports them.
+- For scenes with multiple elements, define foreground, midground, background, relative scale, and key spatial relationships only when they materially improve the result.
@@
 ## Text in images
 - Put literal text in quotes or ALL CAPS and specify typography (font style, size, color, placement).
 - Spell uncommon words letter-by-letter if accuracy matters.
 - For in-image copy, require verbatim rendering and no extra characters.
+- Specify visual treatment and integration: font style or family, weight, relative size, placement, and what surface or object carries the text.
@@
 ## Input images and references
 - Do not assume that every provided image is an edit target.
 - Label each image by index and role (`Image 1: edit target`, `Image 2: style reference`).
 - If the user provides images for style, composition, or mood guidance and does not ask to modify them, treat the request as generation with references.
 - If the user asks to preserve an existing image while changing specific parts, treat the request as an edit.
 - For compositing, describe how the images interact (`place the subject from Image 2 into Image 1`).
+- For recurring characters or subjects across variants, preserve at least 3 distinctive visual markers so the identity stays recognizable.
@@
 - historical-scene: State the location/date and required period accuracy; constrain clothing, props, and environment to match the era.
+- historical-scene: If period or cultural accuracy cannot be supported confidently, flag the uncertainty instead of guessing.
 
 Edit:
 - text-localization: Change only the text; preserve layout, typography, spacing, and hierarchy; no extra words or reflow unless needed.
*** End Patch
```

### Mandatory interpretation rules for this diff

- The implementing agent must apply **only** the bullet additions shown above.
- No new heading may be introduced.
- The `## Contents` section in [patch-imagegen/prompting.patched.md](prompting.patched.md) must remain unchanged in this task.
- The file must remain structurally aligned with its current heading map.
- Existing execution-layer defaults must remain intact.

## Exact instruction for [patch-imagegen/sample-prompts.patched.md](sample-prompts.patched.md)

### Reason

This file already preserves breadth, keeps runtime policy outside prompt scaffolds, and does not currently need a change to absorb the selected virtues from the external skill.

### Required action

**No patch must be applied** to [patch-imagegen/sample-prompts.patched.md](sample-prompts.patched.md) in this task.

### Mandatory interpretation rules

- Do not add examples.
- Do not rewrite existing prompts.
- Do not “improve” wording opportunistically.
- Do not touch the execution-default notes at the top.

## Acceptance criteria

The task is complete only if all of the following are true:

1. [patch-imagegen/SKILL.patched.md](SKILL.patched.md) contains exactly one new ambiguity-control bullet in `### Prompt augmentation policy`.
2. [patch-imagegen/prompting.patched.md](prompting.patched.md) receives the six conceptual additions specified in the diff above and no structural expansion.
3. [patch-imagegen/prompting.patched.md](prompting.patched.md) receives no new headings and no TOC changes.
4. [patch-imagegen/sample-prompts.patched.md](sample-prompts.patched.md) remains unchanged.
5. No file introduces “positive descriptions only” as a rule.
6. No file replaces labeled prompt scaffolds with paragraph-only output requirements.
7. No file embeds runtime attributes into prompt scaffolds.
8. The patched set remains aligned with the validated default use of [mcp-image](../src/server/mcpServer.ts:225).

## Failure conditions

The implementation must be considered incorrect if any of the following happens:

- the agent adds extra edits not present in this document
- [patch-imagegen/sample-prompts.patched.md](sample-prompts.patched.md) is modified
- [patch-imagegen/prompting.patched.md](prompting.patched.md) receives a new heading or TOC rewrite
- the patched set drifts toward a generic prompt-only skill disconnected from [mcp-image](../src/server/mcpServer.ts:225)
- the agent interprets this document as permission to do additional cleanup beyond the explicit diffs above

## Execution note

The next agent must treat this file as the full source of truth for this integration task. If something is not explicitly requested here, it must not be changed.
