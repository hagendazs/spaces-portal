# Growth loop log — newest first. Every agent reads this before acting.

## 2026-07-02 · setup
- Phase 1 initialized: web agent charter (`agents/web.md`) + workflow
  (`.github/workflows/agents.yml`, Mondays 09:00 Taipei).
- Vertical page queue: 零售 → 倉儲 → 辦公 → 社區 → 住家 (one per run).
- Known open finding for web agent run #1: canonicals/sitemap use extensionless
  URLs but GitHub Pages serves `.html` — extensionless paths 404 until the
  production host adds rewrites.
- Phase 2 (grow: Plausible + Formspree) and Phase 3 (ads) not yet wired.
