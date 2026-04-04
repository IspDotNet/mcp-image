# Patch Brief for the `imagegen` Skill

## Objective

Update the existing `imagegen` skill so that it remains a general-purpose skill for helping the agent generate and edit good raster images, while teaching the agent to use the already-installed `mcp-image` MCP server correctly as the default execution path for actually producing those images.

This patch is **not** about rewriting the skill from scratch, changing its scope, or changing unrelated instructions. The work must be narrowly focused on preserving the skill's identity while aligning its operational guidance to the validated MCP-based workflow, without scattering runtime policy across the whole skill.

The files to update are only:
- `SKILL.md`
- `references/prompting.md`
- `references/sample-prompts.md`

Do **not** modify any other files.

---

## Why this patch is needed

The current `imagegen` skill still reflects older execution assumptions and no longer encodes the preferred workflow that was validated during testing. During testing, a separate MCP server named `mcp-image` was installed and validated successfully on Windows 11, then configured globally in Roo Code.

The MCP is now the preferred path because:

1. It is operational in this environment.
2. It can generate and edit images successfully from Roo Code.
3. It gives better operational control than the skill's older execution assumptions.
4. The server can be configured with `SKIP_PROMPT_ENHANCEMENT=true`, which materially improves prompt fidelity for style-sensitive tasks.
5. We validated that prompt fidelity is often better when:
   - the MCP is used directly,
   - automatic prompt enhancement is skipped,
   - the prompt is explicit,
   - and `quality: "balanced"` is used as the default.

In short: the skill must continue teaching the agent how to generate good images, but it must also encode that the default tool used to execute that work is `mcp-image`, with that operational policy kept mainly in the appropriate execution-oriented section rather than spread throughout the whole skill.

---

## Important context from prior testing

### 1. Platform and installation findings

The `mcp-image` package was verified to work on Windows 11.

What was validated:
- Node and npm were present and compatible.
- The published npm package could be installed globally.
- The server could start successfully on Windows.
- The MCP could be registered globally in Roo Code.
- Roo could generate real images through the MCP.

Validated global binary path on this PC:

`%APPDATA%\npm\mcp-image.cmd`

Default output directory used in testing:

`%USERPROFILE%\mcp-image-output`

---

### 2. Roo Code MCP configuration that was validated

The MCP was configured globally in Roo Code with:

- command pointing to the validated Windows npm shim
- `GEMINI_API_KEY` supplied through Roo's MCP editor
- `IMAGE_OUTPUT_DIR` set to the tested local output folder
- `SKIP_PROMPT_ENHANCEMENT` set to `true`

That last flag is important.

`SKIP_PROMPT_ENHANCEMENT=true` is the key reason the MCP became more faithful to the prompt wording. Without it, the server's internal prompt optimizer tended to reinterpret some style-sensitive prompts too aggressively.

This is one of the central reasons the skill must now be updated.

---

### 3. Image-quality conclusions reached during testing

The following conclusions were reached and must shape the skill:

#### Default quality preset

Use `balanced` by default.

Reason:
- It gave the best balance between obedience and quality.
- It was more faithful than the highest-end preset in some style-sensitive cases.

#### When to use `quality`

Use `quality` only when the user explicitly asks for maximum quality or when the task clearly benefits from it and the extra cost is acceptable.

Reason:
- The `quality` preset improved finish and polish.
- But in testing it also increased the tendency to reinterpret the subject or over-refine the output.

#### When to use `fast`

Use `fast` only for drafts, quick exploration, or cost/speed-sensitive iteration.

---

### 4. Findings about `useWorldKnowledge` and `useGoogleSearch`

These must **not** become defaults.

#### `useWorldKnowledge`

Use only when the request depends on factual, historical, cultural, geographical, or landmark accuracy.

Do **not** enable it for ordinary fictional scenes, stylized illustrations, generic product images, or ordinary concept art.

Testing showed that it is not the main lever for obedience in artistic prompts.

#### `useGoogleSearch`

Use only when the request depends on current or web-grounded facts.

Do **not** enable it for timeless style requests, non-current scenes, or ordinary artistic generation.

---

### 5. Findings about prompt fidelity

The MCP worked best when:
- prompt enhancement was skipped,
- prompts were explicit,
- the visual medium was stated as a hard constraint,
- and the request did not rely on vague post-hoc correction via `purpose`.

This means the skill should teach the agent to:
- keep prompts explicit,
- preserve medium/style constraints rigidly,
- avoid unnecessary creative augmentation,
- and use MCP parameters intentionally rather than mechanically.

---

## Constraints for this patch

The agent performing this task must follow these constraints carefully:

1. Do **not** rewrite the skill from scratch.
2. Do **not** change the skill's identity, name, or broad purpose.
3. Do **not** modify any files other than:
   - `SKILL.md`
   - `references/prompting.md`
   - `references/sample-prompts.md`
4. Do **not** introduce unrelated workflow changes.
5. Do **not** add provider-specific installation logic into the skill instructions themselves.
6. Do **not** remove useful prompt-structure guidance that is still valid.
7. Preserve the skill as a practical operational guide, not a generic essay.
8. Do **not** recast the skill as if its primary identity were “a skill for mcp-image”.
9. Keep the skill primarily about how to request good images; treat `mcp-image` as the default execution mechanism.
10. Do **not** scatter MCP runtime policy redundantly across multiple sections when it can be centralized in the execution-oriented section.
11. Do **not** reduce the breadth of useful examples or task coverage that existed in the original references unless there is a strong, explicit reason.

This is a targeted patch, not a redesign.

---

## What must change in `SKILL.md`

### A. Frontmatter description

Update the description so it first describes the skill as a raster image generation/editing skill, and only then clarifies that `mcp-image` is the default execution path.

The description should also reflect these points:
- it is for raster image generation/editing,
- it defaults to `mcp-image` operationally,
- it prefers `balanced` quality unless the user asks otherwise,
- and it should preserve prompt fidelity for style-sensitive tasks.

Important refinement:
- Keep the description concise.
- Do not overload it with too many concerns in one sentence.
- It should be easy for an agent to understand first **what the skill is for**, and only secondarily **what tool it should use by default**.
- Do **not** include lower-level runtime assumptions such as `SKIP_PROMPT_ENHANCEMENT=true` in the frontmatter description.

Do not rename the skill.

---

### B. `## When to use`

Keep the general use cases, but change the operational framing.

The section must make clear that:
- the skill is for AI-generated or AI-edited bitmap images,
- and, once the skill is invoked, the default execution tool should be `mcp-image`.

Important nuance:
- The section must **not** read as if the skill exists only for `mcp-image`.
- The skill should be discoverable and usable by an agent that first needs help with image generation in general.
- The MCP choice comes after the skill is invoked, as implementation guidance.
- Do **not** place detailed runtime policy here if it belongs in `Execution notes`.

It is acceptable to add a short “Typical cases” subsection or bullet list.

---

### C. `## Inputs required`

Keep the current input requirements, but add explicit mention that the agent should also consider MCP-specific overrides when relevant, such as:
- `quality`
- `useWorldKnowledge`
- `useGoogleSearch`

This should remain concise.

---

### D. Add a new section for default execution behavior

Do **not** introduce a large standalone section that duplicates execution policy if that same content can be expressed more cleanly in `Execution notes`.

If a short execution-default note is retained outside `Execution notes`, it must be minimal and must not duplicate the detailed policy.

The detailed runtime policy should live primarily in `Execution notes`, including:

1. Use `mcp-image` by default.
2. Assume `SKIP_PROMPT_ENHANCEMENT=true` is already configured in Roo.
3. Default to `quality: "balanced"`.
4. Use `useWorldKnowledge: true` only for factual/historical/cultural/geographic accuracy needs.
5. Use `useGoogleSearch: true` only for current or web-grounded facts.
6. Treat `purpose` as optional guidance, not as a replacement for a good prompt.

---

### E. `## Workflow`

Keep the existing decision flow, but replace the outdated execution instruction with concise MCP-based execution guidance.

The workflow should now say, in substance:
- classify generate vs edit,
- classify single output vs variants,
- normalize the prompt without unnecessary creative drift,
- label input-image roles,
- specify invariants for edits,
- use `mcp-image` as the default execution path,
- pass `quality: "balanced"` unless the user asked otherwise,
- add factual parameters only when they materially help,
- iterate with small changes if needed.

Do not turn the workflow into a long prose block. Keep it operational and ordered.

Important nuance:
- The workflow should still read like an image-generation skill workflow.
- It should not read like MCP-only tool documentation.
- Do not duplicate detailed runtime assumptions here if they are already specified in `Execution notes`.

---

### F. Add a new section about MCP invocation rules

Avoid adding a separate rules section if it creates duplication with `Execution notes`.

If a separate rules section is kept, it must be extremely compact and must not repeat runtime policy already covered elsewhere.

It must clearly state:
- default: `quality: "balanced"`
- no `useWorldKnowledge` by default
- no `useGoogleSearch` by default
- use `quality: "quality"` only when explicitly requested or clearly justified
- use `quality: "fast"` for drafts/speed
- do not rely on `purpose` to rescue a weak prompt

This section is important because the whole reason for this patch is to avoid forcing the human to re-specify the execution strategy every time.

---

### G. Examples section

Update the examples so they align with the validated `mcp-image` usage while still reading like image-request examples first.

At minimum:
- add `Quality: balanced` to the examples where it makes sense
- keep examples concise and usable

Do not overload `SKILL.md` with too many examples; detailed templates belong in the references.

### I. `## Files`

Refine the descriptions under `## Files` so they are not generic leftovers from the previous version.

They should explicitly communicate:
- that `references/prompting.md` explains both image-request quality guidance and the validated default execution behavior
- that `references/sample-prompts.md` provides copy/paste prompt scaffolds aligned with the validated default workflow

Do not leave those descriptions in an overly generic or inherited form.

---

### H. `## Execution notes`

This section needs significant revision.

It should explicitly instruct the agent to:
- use `mcp-image` as the default image-generation mechanism
- assume `SKIP_PROMPT_ENHANCEMENT=true` in the validated Roo configuration, so the agent should strengthen its own prompt instead of relying on MCP-side prompt enhancement
- assume `balanced` unless told otherwise
- use `quality: "quality"` only when the user wants maximum fidelity or accepts extra cost
- keep `useWorldKnowledge` off unless the task depends on factual, historical, cultural, or geographic accuracy
- keep `useGoogleSearch` off unless the task depends on current or web-grounded facts
- save assets into the requested project path when needed
- avoid overwriting unless explicitly requested
- consult `references/prompting.md` and `references/sample-prompts.md` when needed

This section should be treated as the **main home** for the detailed execution policy.

Important refinement:
- prefer concentrating MCP runtime assumptions here instead of distributing them redundantly through frontmatter, `When to use`, workflow, and multiple policy sections.
- the skill should still know how to invoke the MCP correctly, but that knowledge should be localized here as much as possible.

---

## What must change in `references/prompting.md`

This file currently contains guidance that still refers to outdated execution assumptions.

It must be updated so that it still teaches good prompting in general, while aligning the operational details to `mcp-image`.

### Required changes

1. Replace framing that assumes an older default execution path.
2. Remove or replace language about CLI fallback where it no longer applies.
3. Keep the document centered on prompting quality, not on MCP identity.
4. Add a clear explanation of the default execution behavior once the skill is active:
   - `mcp-image` is the default execution path
   - `quality: "balanced"`
   - no `useWorldKnowledge` by default
   - no `useGoogleSearch` by default
5. Add or strengthen guidance that style/medium constraints are hard constraints.
6. Add a short parameter-guidance section for:
   - `quality`
   - `useWorldKnowledge`
   - `useGoogleSearch`
   - `purpose`
7. Add the tested rule that if `quality: "quality"` improves finish but reduces obedience, step back to `balanced`.
8. Ensure the table of contents stays consistent with the actual section headings after the patch.

Important refinement:
- Keep runtime policy present but compact.
- Do not let the document become more about tool invocation than about prompting quality.

### Preserve from the existing file where still useful

Keep the good parts that remain valid, especially:
- prompt structure
- specificity policy
- constraint and invariant handling
- text handling
- reference-image role labeling
- iterative refinement discipline

Do not destroy useful content unnecessarily.

---

## What must change in `references/sample-prompts.md`

This file should be updated so the examples reflect the validated `mcp-image` execution defaults without ceasing to be prompt examples first.

### Required changes

1. Keep the document as example prompts for image generation and editing.
2. Add a short note near the top that the default execution path is `mcp-image`.
2. Add a short default-invocation note near the top:
   - `quality: "balanced"`
   - no `useWorldKnowledge`
   - no `useGoogleSearch`
3. Update existing examples to include quality where appropriate.
4. Add at least one style-sensitive example that reflects the lessons from testing.

The most useful example to add is a retro 2D cartoon example with hard medium constraints, such as:
- strictly 2D hand-drawn cartoon
- flat cel-painted colors
- minimal shading
- no 3D rendering
- no volumetric lighting

5. Add at least one factual/historical example showing justified use of `useWorldKnowledge: true`.
6. Keep examples practical and copy-paste friendly.

Important nuance:
- The opening lines of this file must remain subordinate to the main purpose of the document: helping the agent request good images clearly.
- Do not let the intro read as if the file were primarily tool documentation.

Critical preservation requirement:
- Do **not** reduce the breadth of useful example categories that existed in the original file.
- Preserve the original coverage as much as possible, including areas such as UI/wireframes, logo/brand work, lighting/weather edits, style transfer, compositing, sketch-to-render, and concrete text-localization examples.
- New validated examples may be added, but they should be added **without unnecessarily replacing** valuable legacy examples.

Important refinement about execution attributes:
- Do **not** treat `Quality` as part of the prompt scaffold inside `references/sample-prompts.md`.
- `Quality` is an execution/runtime concern of the MCP workflow, not part of the semantic image request itself.
- Keep quality policy in the skill's execution-oriented guidance, not embedded throughout the prompt examples.
- If the top of the file includes a short default-execution note, that is acceptable; however, the individual prompt recipes themselves should remain focused on the image request unless there is a very strong reason to surface an execution override.

### Preserve where possible

Preserve the existing useful examples and their category coverage by default. Update them only as needed to align terminology and defaults with the validated MCP workflow, but do not remove or collapse them unless there is a strong, explicit reason.

---

## What the agent must NOT do

The implementing agent must **not**:

- rewrite all content stylistically just because it can
- change the skill name
- change the skill's overall purpose
- add unrelated installation docs to the skill itself
- hardcode secrets anywhere
- change files outside the three target markdown files
- introduce speculative behavior that was not validated

This task is a patch, not a refactor of the whole skill system.

---

## Installation and configuration notes to preserve for future replication

These notes are **not** meant to be copied into the skill files. They are included here so another agent or operator can reproduce the working setup on another Windows PC.

### Global npm installation

Validated command used to install globally:

```bat
npm install -g mcp-image
```

Validated binary path on this PC after global installation:

```text
C:\Users\Consisnet Lenovo\AppData\Roaming\npm\mcp-image.cmd
```

### Roo Code global MCP registration

The MCP was configured globally in Roo Code to call the Windows npm shim above.

The global Roo Code MCP configuration file is:

`%APPDATA%\Code - Insiders\User\globalStorage\rooveterinaryinc.roo-cline\settings\mcp_settings.json`

Important clarification:

That path corresponds to **Visual Studio Code Insiders** on this PC. In other IDE distributions where the Roo Code extension may be installed, this segment of the path can vary:

`%APPDATA%\Code - Insiders`

Examples:
- Visual Studio Code (stable/release) may use a different application folder name under `%APPDATA%`.
- VS Code forks such as Cursor, Windsurf, Kiro, and similar distributions may also use different application-specific folders.

Therefore, when replicating this setup on another machine or another IDE distribution, keep the remainder of the Roo global settings path structure in mind but verify the IDE-specific base folder under `%APPDATA%` before configuring the MCP.

Validated command path used on this PC:

```text
%APPDATA%\npm\mcp-image.cmd
```

Validated output directory used on this PC:

```text
%USERPROFILE%\mcp-image-output
```

Required env values in Roo MCP config:

```json
{
  "GEMINI_API_KEY": "PASTE_YOUR_API_KEY_HERE",
  "IMAGE_OUTPUT_DIR": "%USERPROFILE%\\mcp-image-output",
  "SKIP_PROMPT_ENHANCEMENT": "true"
}
```

### Why `SKIP_PROMPT_ENHANCEMENT=true` matters

This setting materially improved prompt fidelity.

Without it, the MCP's internal prompt optimizer tended to add helpful-looking but sometimes obstructive reinterpretation, especially in style-sensitive requests such as retro 2D cartoon prompts.

With it enabled, the generated images became more faithful to the exact wording and stylistic constraints of the prompt.

---

## Validated behavioral conclusions that must shape the skill

These conclusions were reached through actual testing and should guide the patch:

1. `balanced` is the best default.
2. `quality` can improve finish but may reduce obedience.
3. `useWorldKnowledge` is useful for factual accuracy, not for general obedience.
4. `useGoogleSearch` is only for current/web-grounded facts.
5. Explicit prompts with hard medium constraints work better than vague prompts.
6. The skill should encode execution behavior so the user does not need to repeatedly specify “use the MCP” or “use balanced by default.”

---

## Final implementation instruction

Apply only the patch described above.

Do not expand scope.
Do not rewrite unrelated content.
Do not modify other files.
Keep the skill practical, operational, and faithful to the validated MCP workflow.
