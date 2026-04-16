# Development Workflow

> This file extends [git-workflow.md](./git-workflow.md) with the full feature development process that happens before git operations.

The Feature Implementation Workflow describes the development pipeline: research, planning, TDD, code review, and then committing to git.

## Feature Implementation Workflow

1. **Research & Reuse** _(mandatory before any new implementation)_
   - **If `docs/product/<feature>.md` exists**, read it first: MVP scope, feature priorities, competitor analysis, and technical constraints come from the PM stage
   - **If `docs/designs/<feature>/` exists**, read design artifacts next: `intent.md` for design decisions, approved `screenshots/` for visual target, and `review-verdict.md` for review findings
   - **GitHub code search first:** Run *GitHub MCP* to find existing implementations, templates, and patterns before writing anything new.
   - **Library docs second:** Use Context7 or primary vendor docs to confirm API behavior, package usage, and version-specific details before implementing.
   - **Use docs-lookup when behavior is unclear:** If framework, API, or third-party library behavior is uncertain, run **docs-lookup** agent before implementing.
   - **Exa only when the first two are insufficient:** Use Exa for broader web research or discovery after GitHub search and primary docs.
   - **Check package registries:** Search npm, PyPI, crates.io, and other registries before writing utility code. Prefer battle-tested libraries over hand-rolled solutions.
   - **Search for adaptable implementations:** Look for open-source projects that solve 80%+ of the problem and can be forked, ported, or wrapped.
   - Prefer adopting or porting a proven approach over writing net-new code when it meets the requirement.

2. **Plan First**
   - Use **planner** agent to create implementation plan
   - Save planning doc to `docs/plans/<feature-name>.md` before coding
   - **If `docs/designs/<feature>/components/*.md` exists**, reference component contracts (variants, states, responsive rules, accessibility, implementation mapping) in the plan
   - **If `docs/designs/<feature>/review-verdict.md` exists**, account for review findings and constraints in the plan
   - Identify dependencies and risks
   - Break down into phases
   - **[Hard Gate]** Present the plan to the user for approval before proceeding — **do not write any code until the user explicitly approves**
   - _Optional:_ For complex plans, run **design-review** skill before approval for adversarial review (Claude Code users can also use **design-review-codex** for independent cross-model analysis)

3. **TDD Approach**
   - If fixing a bug, run **investigate** skill first before writing any fix code
   - Use **tdd-guide** agent
   - **If `docs/designs/<feature>/tokens/` exists**, validate that implementation uses token-derived classes (e.g. `bg-primary`, `rounded-md`) not hardcoded values (e.g. `bg-[#hex]`)
   - **If `docs/designs/<feature>/components/*.md` exists**, write tests that cover the documented states, variants, and accessibility requirements
   - Write tests first (RED)
   - Implement to pass tests (GREEN)
   - Refactor (IMPROVE)
   - Critical user flow changes: add/update E2E coverage and run **e2e-runner** agent
   - Verify 80%+ coverage

4. **Quality Gate** _(after editing files)_
   - Run **code-quality-gate** skill after writing or modifying source files
   - Format all modified files (Biome/Prettier/Ruff/Black)
   - Lint and auto-fix issues (Biome/ESLint/Ruff)
   - Run type checker (tsc/mypy)
   - Remove debug artifacts (console.log, debugger, print statements)
   - Fix all errors before proceeding to code review

5. **Code Review** _(MANDATORY gate before commit)_
   - **This step is NON-NEGOTIABLE.** Every implementation session MUST pass through code review before committing.
   - **Trigger point:** After all source files are written/modified and Quality Gate (step 4) is green, Claude MUST immediately invoke the appropriate review agent(s) — do NOT proceed to Documentation Decision (step 6) or Commit (step 7) without completing this step.
   - Claude MUST run **code-reviewer** agent immediately after writing code, before any `git commit` or `git push`
   - Python projects: also run **python-reviewer** agent
   - TypeScript/Next.js projects: also run **typescript-reviewer** agent
   - Auth, input validation, public endpoints, HTML rendering, file upload, or secret/env handling changes: also run **security-reviewer** agent
   - Address CRITICAL and HIGH issues before proceeding
   - Fix MEDIUM issues when possible
   - Do NOT commit until review is complete and blockers are resolved
   - **Self-check before commit:** Ask yourself: "Did I run code-reviewer on this code?" If the answer is no, stop and run it now.

6. **Documentation Decision** _(before commit)_
   - Delete `docs/plans/<feature-name>.md` — planning docs are process artifacts, not long-term knowledge
   - Create/update `docs/modules/<module>.md` if **any** of the following apply:
     - New standalone module (new package / Django app)
     - New external integration (third-party API or service)
     - New tech stack component (new database, message queue, etc.)
   - If a module doc is created, also update the module table in `docs/index.md`
   - If none of the above apply, ensure code comments are sufficient — no new doc needed

7. **Commit & Push**
   - Run **commit-quality** skill before committing
   - Validate commit message format (Conventional Commits)
   - Lint staged files only
   - Scan for debug artifacts (console.log, debugger, print)
   - Scan for secrets (API keys, tokens, passwords)
   - Never use `--no-verify` to bypass hooks
   - See [git-workflow.md](./git-workflow.md) for commit message format and PR process

8. **Pre-Review Checks**
   - Verify all automated checks (CI/CD) are passing
   - Resolve any merge conflicts
   - Ensure branch is up to date with target branch
   - Only request review after these checks pass
