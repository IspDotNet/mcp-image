---
name: imagegen
description: Generate or edit raster images when the task benefits from AI-created bitmap visuals such as photos, illustrations, textures, sprites, mockups, transparent-background cutouts, or image variants. Use this skill to help the agent produce strong image requests and edits; when executing them, use `mcp-image` as the default path with `balanced` quality unless the user requests otherwise.
---

# Image Generation

## When to use

Use this skill when the task needs AI-generated or AI-edited bitmap images, including website assets, UI mockups, product mockups, game assets, concept art, infographics, transparent-background cutouts, image variants, or transformations of an existing image.

Typical cases:
- illustrations, concept art, story scenes, portraits, photos, mockups, textures, sprites
- image edits, background replacement, style transfer, compositing, text replacement, cutouts
- style-sensitive requests where prompt fidelity and direct control over the final result matter

## When NOT to use

Do not use this skill for:
- SVG, icon, logo-system, or illustration-library work that should stay vector or code-native.
- Simple diagrams, wireframes, or layout explorations that are better built directly in HTML, CSS, canvas, or SVG.
- Small edits to an existing source asset when the native editable source already exists in the repository.
- Tasks where the user clearly wants deterministic non-image output rather than a generated bitmap.

## Inputs required

1. Whether the request is a new image or an edit of an existing image.
2. Whether the task is for one asset or multiple variants.
3. The intended use of the image, if known: hero image, product mockup, concept art, infographic, wireframe, sprite, etc.
4. Any hard constraints: exact text, transparency, preserve subject identity, keep background, no watermark, specific dimensions, or destination path.
5. Any execution overrides that materially affect the result, such as `quality`, `useWorldKnowledge`, or `useGoogleSearch`.

## Workflow

1. Classify the task as `generate` or `edit`.
2. Classify the output scope as one final asset or a set of variants.
3. If the prompt is generic, improve it only enough to materially help the result.
4. If the prompt is already detailed, normalize it into a clean image specification instead of adding creative requirements.
5. For edits, explicitly list invariants such as `change only X; keep Y unchanged`.
6. If the user supplied images, label each one by role before proceeding:
   - edit target
   - style reference
   - composition reference
   - subject reference
7. Execute the request through `mcp-image` as the default execution path for this skill.
8. Use `quality: "balanced"` unless the user asked for a different preset, and keep other execution overrides minimal and intentional.
9. Add factual execution parameters only when they materially improve correctness.
10. If the image is meant for project use, save it into the requested project path or a safe sibling filename.
11. Do not overwrite an existing asset unless the user explicitly asked for replacement.
12. If the first result is close but not correct, iterate with one targeted change at a time.

## Decision rules

### Intent
- Treat the request as `edit` when the user wants to preserve parts of an existing image while changing specific elements.
- Treat the request as `generate` when the user provides no image or provides images only as style, composition, or mood references.

### Scope
- Use a single output when the request has a clear final target.
- Use variants when the user is exploring visual directions, comparing styles, or selecting among alternatives.

### Prompt augmentation policy
- Add only the details needed to improve the output materially.
- If the requested visual medium is ambiguous, clarify it before adding medium-specific detail.
- Do not invent extra props, characters, brand elements, side-specific placement, or story details unless implied.
- For prompts containing exact text, require verbatim rendering.
- For edits, repeat invariants during each iteration to reduce drift.

## Files

- [references/prompting.md](references/prompting.md): Prompt structure, specificity, invariants, text handling, reference-image handling, iteration guidance, and concise execution-layer defaults for the validated MCP workflow.
- [references/sample-prompts.md](references/sample-prompts.md): Copy/paste prompt scaffolds covering the original task breadth while aligning examples to the validated default execution workflow.

## Prompting checklist

1. State the intended use.
2. Define the scene or backdrop.
3. Define the subject.
4. Add the key details that materially affect the result.
5. Add constraints and invariants.
6. If text must appear in the image, quote it exactly and require verbatim rendering.
7. If multiple input images exist, identify each image by role.

## Examples

### Generation example
```text
Use case: product-mockup
Asset type: landing page hero
Primary request: a minimal hero image of a ceramic coffee mug
Style/medium: clean product photography
Composition/framing: wide composition with usable negative space for page copy if needed
Lighting/mood: soft studio lighting
Constraints: no logos, no text, no watermark
```

### Edit example
```text
Use case: precise-object-edit
Asset type: product photo background replacement
Primary request: replace only the background with a warm sunset gradient
Constraints: change only the background; keep the product and its edges unchanged; no text; no watermark
```

## Execution notes

- Use `mcp-image` as the default generation/editing mechanism when this skill is active.
- Assume `SKIP_PROMPT_ENHANCEMENT=true` in the validated Roo configuration, so strengthen your own prompt instead of relying on MCP-side prompt optimization.
- Assume `balanced` is the default quality unless the user asks for a different preset.
- Use `quality: "quality"` only when the user explicitly wants maximum fidelity or accepts higher cost.
- Use `quality: "fast"` for drafts, exploration, or speed-sensitive iteration.
- Keep `useWorldKnowledge` off unless the task depends on factual, cultural, historical, or geographic accuracy.
- Keep `useGoogleSearch` off unless the task depends on current or web-grounded facts.
- Treat `purpose` as optional execution guidance, not as a substitute for a good prompt.
- Do not introduce unrelated CLI/provider-specific flows into this skill.
- If a project path is required, place the final approved asset there before finishing.
- When replacement was not explicitly requested, create a sibling versioned filename such as `hero-v2.png`.
- Read [references/prompting.md](references/prompting.md) when refining prompts or handling complex edits.
- Read [references/sample-prompts.md](references/sample-prompts.md) when the user needs a concrete prompt scaffold.
