# Design Workflow

> This file defines the optimized design workflow v2 for UI work using Pencil MCP and Style Dictionary.
> The design workflow runs alongside the [development workflow](./development-workflow.md)
> and hands approved artifacts to development at the final review gate.

## When to Activate

**Activate when:**

- New UI feature or significant UI change
- Design system creation or update
- Component library work
- Visual or layout decisions are required
- Frontend implementation involving Next.js + TypeScript

**Skip when:**

- Backend-only work
- Configuration changes without visual impact
- Documentation-only changes
- Refactoring without visual changes
- No UI is involved

## Prerequisites

- Pencil MCP server running locally (desktop client or IDE extension)
- [pencil-design skill](../skills/pencil-design/SKILL.md) installed
- `style-dictionary` and `style-dictionary-utils` available for the token pipeline

---

## Core Rules

The v2 workflow keeps the pipeline strict where it matters and lighter everywhere else.

1. **Route UI work up front.**
   - Small UI tweaks go through `L1`.
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

---

## Directory Layout

All design artifacts live under `docs/designs/<feature>/`. See pencil-design skill for full directory structure and file naming conventions.

**Git rules:**

- Add `docs/designs/**/screenshots/.tmp/` to `.gitignore`.
- Only promote screenshots from `.tmp/` to `screenshots/` after approval.
- `intent.md` is long-term knowledge. Keep it for the life of the feature.
- Commit `.pen`, tokens, components, screenshots; `.tmp/` is gitignored.

---

## Workflow Levels

### L1. Lightweight UI Change

Use `L1` when all of the following are true:

- No new page or major layout pattern
- No new interaction model
- No new design-system component
- No new token is required

**Actions:**

1. Confirm the change is local and does not introduce new design primitives.
2. Capture before/after screenshots for the affected area.
3. Record a short note in `intent.md` or the PR description:
   - what changed
   - why it changed
   - which existing tokens/components were reused
4. Proceed directly to the development workflow Step 2 and Step 3.

**Outputs:**

- Updated screenshot(s) for the affected surface
- A short design note in `intent.md` or the PR

**Escalation rule:** If the change grows into a new page, new interaction, or new token, upgrade immediately to `L2`.

### L2. Standard Design Workflow

Use `L2` for:

- New pages or substantial layout changes
- New reusable components
- Complex states or interaction flows
- Design-system updates
- Any change that introduces or expands tokens

The standard path has five stages and three gates.

---

## L2 Standard Workflow

### V2-1. Design Intent

Investigate requirements and establish design direction before editing the canvas.

**Actions:**

- **If `docs/product/<feature>.md` exists**, read it first as the primary input: UI scope, user flows, feature list, competitor references, and design constraints come from the PM stage
- Analyze the UI scope: pages, components, interactions, and constraints
- Search the project for reusable design assets and existing `docs/designs/` directories
- Use `pencil_batch_get` with `patterns: [{ reusable: true }]` to discover reusable components
- Use `pencil_get_variables` to inspect the existing token system
- Write the design intent, references, direction, and decision rationale

**Output:**

- `docs/designs/<feature>/intent.md`

**Gate 1:** User confirms the design direction.

---

### V2-2. Wireframe + Baseline Tokens

Create the page structure and establish the minimum token set needed to support it. This stage merges the old token-baseline step and wireframing step so the team does not stop twice before layout approval.

**Actions:**

1. Define/confirm baseline tokens (brand, semantic, surface, typography, radius) — see pencil-design skill for complete token reference.
2. Verify tokens with `pencil_get_variables`.
3. Build the wireframe and page regions using reusable components and tokenized values only.
4. Capture review screenshots to `screenshots/.tmp/`.
5. Run `pencil_snapshot_layout({ problemsOnly: true })` on the affected screens.
6. Iterate until the page structure and regions are stable.

**Outputs:**

- `docs/designs/<feature>/wireframes.pen`
- Final approved wireframe screenshot(s) in `docs/designs/<feature>/screenshots/`

**Gate 2:** User confirms layout, page regions, and overall structure.

---

### V2-3. High-Fidelity Design + Token Expansion + Key Component Contracts

Refine the wireframe into the final design, expand the token system, and document only the component constraints that development genuinely needs.

**Actions:**

1. Refine the design to high fidelity in `design.pen`.
2. Expand tokens (spacing, shadows, breakpoints, theme variants) as needed.
3. Run token pipeline — see pencil-design skill.
4. Verify generated outputs (`tokens.css`, `tokens.ts`, `tailwind-preset.ts`).
5. Capture final component and screen screenshots to `screenshots/.tmp/`, then promote approved results.
6. Write component contracts only for key components or screens that need implementation guidance.

**Required sections for each `components/*.md`:**

- `## Variants`
- `## States`
- `## Responsive`
- `## Accessibility`
- `## Implementation Mapping`
- `## Design Constraints`

**Rule:** Do not duplicate full TypeScript props interfaces in markdown unless design decisions directly constrain the public API. Source code remains the authority for props.

**Completion standard:** Key components are covered, and generated token outputs are consistent with `w3c.json`.

---

### V2-4. Design Review [hard gate]

Review all design artifacts before handoff. This is the single hard approval gate in the standard path.

> Playwright visual regression and axe-core accessibility audits still belong to the development workflow Step 3. `V2-4` reviews design-time artifacts only.

**Phase 1 - Design-time visual checks**

1. Capture final screenshots for each documented breakpoint.
2. Run `pencil_snapshot_layout({ problemsOnly: true })` on relevant screens.
3. Save results:
   - approved screenshots -> `docs/designs/<feature>/screenshots/`
   - layout issues -> `docs/designs/<feature>/screenshots/layout-report.md`

**Phase 2 - `design-reviewer` agent**

Run the `design-reviewer` agent against `docs/designs/<feature>/`. The agent checks token coverage, contract completeness, artifact consistency, a11y docs, and responsive coverage.

**Visual quality is still reviewed by humans, not the agent.**

**Phase 3 - User approval**

1. Review the artifact report in conversation.
2. Present final screenshots and review findings.
3. On approval, write the verdict and approval decision to `docs/designs/<feature>/review-verdict.md`.

**Outputs:**

- `docs/designs/<feature>/screenshots/layout-report.md`
- `docs/designs/<feature>/review-verdict.md`
- Design review report in conversation

**Gate 3:** User explicitly approves. Only then does the workflow hand off to development.

---

### V2-5. Handoff to Development Workflow

After `Gate 3`, the design artifacts become inputs to the development workflow.

| Dev Step | Consumes | How |
|----------|----------|-----|
| Step 1 (Research & Reuse) | `docs/product/<feature>.md`, `intent.md`, `design.pen`, screenshots | Read PM output first (MVP scope, priorities, constraints), then confirm design direction and visual target |
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

---

## Version Control Strategy

`.pen` files are binary. They cannot be diffed or merged reliably by git.

- One `.pen` file pair per feature: `wireframes.pen` and `design.pen`
- Keep `wireframes.pen` and `design.pen` separate
- Do not edit the same `.pen` file in parallel on multiple branches

---

## shadcn/ui Fallback Priority

When the mapping table in [design-to-code-workflow.md](../skills/pencil-design/references/design-to-code-workflow.md) does not cover a component, use this priority:

1. `shadcn/ui` official
2. `shadcn/ui` registry
3. `tremor`, `magicui`, `aceternity-ui`
4. Radix UI primitives
5. Handrolled component as the last resort

Do not jump straight to handrolled components.

---

## Execution Layer

Delegates Pencil MCP operations to **pencil-design** skill. Triggers **design-reviewer** agent at V2-4. See [agents.md](agents.md) for agent definitions.
