---
name: design-workflow
description: UI design workflow orchestration (v2). TRIGGER when: new UI feature, new page, new component, or visual change; tasks involving docs/designs/ directories, *.pen files, Pencil MCP tools, or design tokens; reading design artifacts before implementation. Skip for backend-only, config-only, refactoring without visual impact.
metadata:
  author: sre-copilot
  version: "2.0"
---

# Design Workflow v2

> Orchestrates UI design work using Pencil MCP and Style Dictionary.
> Execution-layer Pencil operations are delegated to the **pencil-design** skill.

## Prerequisites

- Pencil MCP server running locally (desktop client or IDE extension)
- [pencil-design skill](../pencil-design/SKILL.md) installed
- `style-dictionary` and `style-dictionary-utils` available for the token pipeline
- (Optional) `DESIGN.md` in the project root — if present, it becomes the visual identity SSOT

---

## Core Rules

1. **Route UI work up front.**
   - Small UI tweaks go through `L1` — see [l1-lightweight.md](references/l1-lightweight.md).
   - New pages, new components, new interactions, or new tokens go through `L2`.
2. **Only three design gates exist in the standard path.**
   - `Gate 1`: direction confirmation
   - `Gate 2`: layout confirmation
   - `Gate 3`: final approval before handoff
3. **Tokens remain the source of truth.**
   - Do not hardcode visual values.
   - Do not edit generated token files by hand.
4. **Design docs capture constraints, not duplicate implementation.**
   - Markdown component docs describe behavior, mapping, responsive rules, accessibility, and visual constraints.
   - Source code owns the final TypeScript props and implementation details.
5. **DESIGN.md is the visual identity authority when it exists.**
   - Read `DESIGN.md` at the start of every V2 stage.
   - Tokens are derived from DESIGN.md (color palette → brand tokens, typography → font tokens, etc.).
   - DESIGN.md is one-way: the design workflow never writes back to it. If a design need falls outside DESIGN.md scope, fix the design, not the file.

---

## DESIGN.md Integration

When `DESIGN.md` exists in the project root, it serves as the visual identity single source of truth (SSOT). The 9 DESIGN.md sections map to three roles:

- **Token derivation** (6 sections): Color palette → color tokens; Typography → font tokens; Layout principles → spacing/radius/breakpoint tokens; Depth and elevation → shadow tokens; Component styles → component-level token defaults; Responsive behavior → breakpoint tokens and touch targets.
- **Validation constraint** (1 section): Do's and don'ts — applied as hard validation rules during V2-3 and V2-4.
- **Context reference** (2 sections): Visual theme and atmosphere, Agent prompt guide — used as ambient context, not tokenized.

See pencil-design skill `tokens-and-variables.md` for the complete mapping table.

---

## Directory Layout

All design artifacts live under `docs/designs/<feature>/`. See pencil-design skill for full directory structure and file naming conventions.

**Git rules:**

- Add `docs/designs/**/screenshots/.tmp/` to `.gitignore`.
- Only promote screenshots from `.tmp/` to `screenshots/` after approval.
- `intent.md` is long-term knowledge. Keep it for the life of the feature.
- **`.pen` files live in the Pencil editor (like Figma cloud). They are NOT committed to `docs/designs/`.** During development, use Pencil MCP to read/write the design directly. Screenshots, tokens, and component contracts are the persistent artifacts in the repo.
- Commit tokens, components, screenshots; `.tmp/` is gitignored.

---

## L2 Standard Workflow

### V2-1. Design Intent

Investigate requirements and establish design direction before editing the canvas.

**Actions:**

- **If `DESIGN.md` exists**, read it first as the visual identity source: color palette, typography, component styles, layout principles, depth rules, do's and don'ts, and responsive behavior become hard constraints for all subsequent stages
- **If `docs/product/<feature>.md` exists**, read it as the functional requirements input: UI scope, user flows, feature list, competitor references, and design constraints come from the PM stage
- Analyze the UI scope: pages, components, interactions, and constraints
- **If `DESIGN.md` exists**, classify components into "matches existing DESIGN.md component styles" vs "requires new component variant"
- Search the project for reusable design assets and existing `docs/designs/` directories
- Use `pencil_batch_get` with `patterns: [{ reusable: true }]` to discover reusable components
- Use `pencil_get_variables` to inspect the existing token system
- Pass the design intent data to → doc-writer agent 模板：`design-intent`

**Output:** `docs/designs/<feature>/intent.md`（通过 doc-writer 写入）

**Gate 1:** User confirms the design direction.

---

### V2-2. Wireframe + Baseline Tokens

Create the page structure and establish the minimum token set needed to support it.

**Actions:**

1. **If `DESIGN.md` exists**, derive baseline tokens from it (see mapping table in pencil-design `tokens-and-variables.md`):
   - Color tokens ← DESIGN.md *Color palette and roles*
   - Typography tokens ← DESIGN.md *Typography rules*
   - Spacing / border-radius tokens ← DESIGN.md *Layout principles*
   - Shadow tokens ← DESIGN.md *Depth and elevation*
   - Component skeleton styles ← DESIGN.md *Component styles*
   - *Visual theme and atmosphere* and *Agent prompt guide* sections serve as context reference only
   - *Do's and don'ts* is not tokenized — it applies as a validation constraint in V2-3 and V2-4
2. If `DESIGN.md` does not exist, define/confirm baseline tokens (brand, semantic, surface, typography, radius) as usual — see pencil-design skill for complete token reference.
3. Verify tokens with `pencil_get_variables`.
4. Build the wireframe and page regions using reusable components and tokenized values only.
5. Capture review screenshots to `screenshots/.tmp/`.
6. Run `pencil_snapshot_layout({ problemsOnly: true })` on the affected screens.
7. Iterate until the page structure and regions are stable.

**Outputs:**

- Final approved wireframe screenshot(s) in `docs/designs/<feature>/screenshots/`

**Gate 2:** User confirms layout, page regions, and overall structure.

---

### V2-3. High-Fidelity Design + Token Expansion + Key Component Contracts

Refine the wireframe into the final design, expand the token system, and document only the component constraints that development genuinely needs.

**Actions:**

1. Refine the design to high fidelity in `design.pen`.
2. **If `DESIGN.md` exists**, enforce its rules as hard guardrails:
   - Component styles must stay within DESIGN.md *Component styles* definitions
   - Colors must stay within DESIGN.md *Color palette and roles* scope
   - DESIGN.md *Do's and don'ts* are non-negotiable constraints
   - Responsive behavior must follow DESIGN.md *Responsive behavior* module
   - Any design element outside DESIGN.md scope must be flagged — fix the design, not the file
2. Expand tokens (spacing, shadows, breakpoints, theme variants) as needed.
3. Run token pipeline — see pencil-design skill.
4. Verify generated outputs (`tokens.css`, `tokens.ts`, `tailwind-preset.ts`).
5. Capture final component and screen screenshots to `screenshots/.tmp/`, then promote approved results.
6. Pass component contract data to → doc-writer agent 模板：`component-contract`（doc-writer 包含完整模板：Variants、States、Responsive、Accessibility、Implementation Mapping、Design Constraints）

**Rule:** Do not duplicate full TypeScript props interfaces in markdown unless design decisions directly constrain the public API. Source code remains the authority for props.

**Completion standard:** Key components are covered, and generated token outputs are consistent with `w3c.json`.

---

### V2-4. Design Review [hard gate]

Review all design artifacts before handoff. This is the single hard approval gate in the standard path.

> Playwright visual regression and axe-core accessibility audits belong to the development workflow Step 3. This stage reviews design-time artifacts only.

**Phase 1 - Design-time visual checks**

1. Capture final screenshots for each documented breakpoint.
2. Run `pencil_snapshot_layout({ problemsOnly: true })` on relevant screens.
3. Pass layout report data to → doc-writer agent 模板：`layout-report`
   - approved screenshots → `docs/designs/<feature>/screenshots/`

**Phase 2 - `design-reviewer` agent**

Run the `design-reviewer` agent against `docs/designs/<feature>/`. The agent checks token coverage, contract completeness, artifact consistency, a11y docs, and responsive coverage.

**If `DESIGN.md` exists**, the agent additionally checks:
- Do all tokens trace back to a DESIGN.md rule?
- Are there colors, fonts, or component variants not defined in DESIGN.md?
- Does the design violate any DESIGN.md *Do's and don'ts*?
- Violations require fixing the design — never extend DESIGN.md to accommodate them.

**Visual quality is still reviewed by humans, not the agent.**

**Phase 3 - User approval**

1. Review the artifact report in conversation.
2. Present final screenshots and review findings.
3. On approval, pass verdict data to → doc-writer agent 模板：`review-verdict`

**Outputs:**

- `docs/designs/<feature>/screenshots/layout-report.md`
- `docs/designs/<feature>/review-verdict.md`
- Design review report in conversation

**Gate 3:** User explicitly approves. Only then does the workflow hand off to development.

---

### V2-5. Handoff to Development Workflow

After Gate 3, design artifacts become inputs to the development workflow.

**If `DESIGN.md` exists**, all handoff artifacts already comply with it (enforced in V2-3 and V2-4). Ensure `intent.md` references DESIGN.md as the visual authority so the development workflow maintains the same constraints.

| Dev Step | Consumes | How |
|----------|----------|-----|
| Step 1 (Research & Reuse) | `intent.md`, `screenshots/`, Pencil MCP | Confirm design direction and visual target; read precise properties via MCP if needed |
| Step 2 (Plan First) | `components/*.md`, `review-verdict.md` | Reference component contracts and review findings in the implementation plan |
| Step 3 (TDD) | `tokens/*`, `components/*.md` | Validate token usage, run Playwright visual regression, run axe-core accessibility audit |

**Implementation-side rule:** If implementation changes the visual contract or reveals a missing token, update `docs/designs/<feature>/` in the same PR. Do not patch around the gap with hardcoded values.

---

## Iteration Loops

- **L1 → L2**: scope expands to new page/component/token → upgrade immediately
- **Missing token**: add in Pencil → re-run pipeline → never edit generated files by hand
- **Review findings (V2-4 → V2-3)**: update design/screenshots/contracts → re-run V2-4
- **Dev discovers gap**: update design artifact in same PR → keep code and design aligned
- **Direction change**: update intent.md with new rationale → restart from V2-1
- **DESIGN.md violation found during V2-3 or V2-4**: fix the design to comply → never modify DESIGN.md to accommodate the design

---

## Version Control Strategy

`.pen` files live in the Pencil editor and are NOT committed to the repo.

- Screenshots, tokens, and component contracts are the version-controlled artifacts
- Do not edit the same design in Pencil on multiple branches simultaneously
- Use Pencil MCP during development to read precise design properties

---

## shadcn/ui Fallback Priority

When the mapping table in pencil-design does not cover a component:

1. `shadcn/ui` official
2. `shadcn/ui` registry
3. `tremor`, `magicui`, `aceternity-ui`
4. Radix UI primitives
5. Handrolled component as the last resort

Do not jump straight to handrolled components.
