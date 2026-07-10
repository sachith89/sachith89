# GitHub Profile README — Living Dashboard Design

**Date:** 2026-07-10
**Repo:** sachith89/sachith89 (profile README repo)
**Approach:** Hybrid (approved) — custom static header + auto-updating sections via GitHub Actions + hosted stat cards.

## Goal

Replace template-style profile (greeting gif, 30-icon skill wall, dino gif) with a distinctive, self-updating dashboard that requires near-zero maintenance.

## Visual identity

- **Palette:** dark base `#0d1117` (GitHub dark), accent gradient teal `#2dd4bf` → blue `#38bdf8`, text `#e6edf3`. Light variants use `#ffffff` base, same accents, text `#24292f`.
- All themed elements (header, stat cards, snake) share this palette.

## Layout (top to bottom)

1. **Header SVG** — hand-crafted, static. Name, tagline: software engineer · Sri Lanka · Java/Spring · learning Kubernetes. Two files: `assets/header-dark.svg`, `assets/header-light.svg`, swapped with `<picture>` + `prefers-color-scheme`.
2. **Recent activity** — last 5 GitHub events (PRs, issues, stars, releases) as text list. Auto-written between `<!--START_SECTION:activity-->` / `<!--END_SECTION:activity-->` markers.
3. **Stats row** — GitHub stats card + top-languages card side by side; streak card below. Hosted `github-readme-stats` / `streak-stats` with URL-param theming matching palette, `hide_border=true`, transparent background.
4. **Snake animation** — contribution-graph snake, dark + light SVG variants, `<picture>`-swapped.
5. **Skills** — one curated row of ~10 shields.io `for-the-badge` badges: Java, Spring, Kubernetes, Docker, AWS, PostgreSQL, Kafka, Redis, React, TypeScript.
6. **Connect** — GitHub / LinkedIn / Instagram / Medium badges (kept from current README) + visitor counter.

## Automation

Two workflows in `.github/workflows/`:

- **`snake.yml`** — `Platane/snk` action. Cron daily + manual dispatch. Outputs `github-contribution-grid-snake.svg` (light) and `-dark.svg` to `output` branch. README references raw URLs on that branch.
- **`activity.yml`** — `jamesgeorge007/github-activity-readme` action. Cron every 12h + manual dispatch. Rewrites activity section between markers, commits with `github-actions` bot identity using default `GITHUB_TOKEN` (needs `contents: write` permission).

No self-hosted services. Stat cards remain on public `github-readme-stats` Vercel instance — accepted trade-off (occasional rate-limit blips) for zero maintenance.

## Files

| File | Action |
|---|---|
| `README.md` | rewrite |
| `assets/header-dark.svg`, `assets/header-light.svg` | new |
| `.github/workflows/snake.yml` | new |
| `.github/workflows/activity.yml` | new |
| `greetings.gif`, `dino.gif` | delete |

## Error handling / edge cases

- Activity action failing → old content stays between markers; README never breaks.
- Snake SVG missing before first workflow run → run `workflow_dispatch` once after push; until then image 404s (acceptable, one-time).
- Stat-card outage → GitHub renders broken-image alt text; transient.

## Testing / verification

- Render README locally (grip or GitHub preview) — but final check is on github.com in both light and dark mode.
- Manually dispatch both workflows once after merge; confirm snake SVGs on `output` branch and activity section populated.
