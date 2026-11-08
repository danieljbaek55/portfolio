# Portfolio

Daniel's portfolio site: static HTML with no build step, served by GitHub Pages from `danieljbaek55/portfolio` (custom domain in `CNAME`).

## Layout

- Pages: `index.html` (home) plus one case study per file: `atlas.html`, `cimx.html`, `contxtiq.html`, `dea.html`, `eitl.html`, `servicestudio.html`. Each page is self-contained, with its CSS and JS inline. Images live in `assets/images/`.
- **Edit in `/Users/danielbaek/portfolio_clone`**, not `/Users/danielbaek/portfolio`. The preview (`.claude/launch.json`, config `portfolio`) serves the clone. Once changes are approved, the orchestrator copies the changed files into `/Users/danielbaek/portfolio` so both copies match.

## Conventions

- Type sizes come only from the seven `--type-*` tokens (`display`, `heading`, `subheading`, `lead`, `body`, `small`, `label`). No ad-hoc font sizes.
- Colors come from the existing tokens (`--text`, `--text-muted`, `--accent`, ...). All text meets WCAG AA contrast; light text sits on a dark surface, never on the light frosted background.
- Fonts are Fraunces and Outfit, from Google Fonts.
- Copy has no em dashes: use commas, periods, colons or parentheses. Date ranges use an en dash; page titles use the middle-dot separator.

## Agent team

The main session is the **orchestrator**. It talks to the user, writes the plan, splits the work, routes every handoff between agents, and verifies the result. Developers build every plan; outside the fast lane below, the orchestrator doesn't implement anything itself. Use these agents, not the built-in `Explore` or `general-purpose` ones:

| Agent | Model | Role |
|---|---|---|
| `explorer` | Haiku 5.5 | Extracts data from the codebase, web or browser. Runs in parallel. Read-only. |
| `developer` | Sonnet 5.5 | Implements one area of the plan. Runs in parallel, one area each. |
| `reviewer` | Opus 5.5, high effort | Reviews every developer handoff against the plan. Read-only. |
| `sr-developer` | Opus 5.5, xhigh effort | Called ad hoc. Diagnoses stuck work and directs the developers. |

Each agent's instructions live in `.claude/agents/<name>.md`. Agents don't spawn or message each other: every handoff goes through the orchestrator. If you are one of these agents, follow your own file; the workflow below is the orchestrator's.

### Fast lane

The orchestrator makes the change itself, with no plan, developer or reviewer, when all of these hold:
- The user said exactly what to change, so no design or wording decision is left to make (fix a typo, swap a link or image path, set a value the user gave).
- It touches one file, or repeats the same mechanical change across pages.
- It adds no new CSS rules, layout or content beyond what the user dictated.

Edit in the clone, check the change against the conventions above, look at each changed page in the preview, copy the files into `/Users/danielbaek/portfolio`, and report. If it turns out bigger than it looked, stop and switch to the full workflow. When in doubt, use the full workflow.

### Workflow

1. **Explore.** When the plan needs facts you don't have, dispatch explorers in parallel, one question or area each. Verify anything the plan will depend on before building on it.
2. **Plan.** Write the plan to `.claude/plans/<slug>.md`: goal, acceptance criteria, design constraints, out of scope, and the work split. Each area of focus names its tasks and the files it owns. Areas never share a file; if two areas need the same file, give it to one of them. Show the plan to the user and get a go-ahead before building.
3. **Build.** Dispatch one developer per area, in parallel, each with the plan path, its area and its files.
4. **Review.** Start one reviewer for the plan when the first handoff arrives, and keep it for the whole plan: resume it with `SendMessage` for every later handoff and round, so it keeps its context. Send it the plan path and the developer's handoff report.
   - **CHANGES REQUESTED:** resume the same developer with the findings, then send its updated report back to the reviewer.
   - **NEEDS CLARIFICATION:** answer from the plan and what the user has said; if neither settles it, ask the user. Never invent an answer. Update the plan file if the answer changes it, then resume the reviewer.
5. **Escalate.** If an area fails review twice, or its developer reports it's blocked, call `sr-developer` with the plan path, the developer's reports, every round of reviewer findings and the files involved. Pass its directions to the developers it names, then send their work back through review. Take any plan issues it raises to the user.
6. **Finish.** When every area is approved, check every changed page in the preview at desktop and mobile width, copy the changed files from the clone into `/Users/danielbaek/portfolio`, and report to the user. Commit or push only when the user asks.
