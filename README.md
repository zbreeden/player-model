# FourTwenty • The Player
> Decision simulation — branching paths, outcomes, and behavior under uncertainty.

A lightweight sandbox that fuses **interactive fiction** (storylets) with **simulation** (simple rules / Monte Carlo). Choices generate **event logs** you can analyze in **Power BI**, with telemetry to **GA4 via GTM**.

---

## What’s inside
- **Story Mode:** YAML-driven storylets that advance state via simple effects.
- **Sandbox Mode:** Sliders/params for quick experiments (runs, risk, WIP limit).
- **Scorecard Mode:** Power BI report over exported run logs.
- **Telemetry:** GTM → GA4 events (`choice_made`, `state_updated`, `run_complete`, …).
- **Artifacts:** CSV logs + screenshots for portfolio/demo.

---

## Quick start
1. Clone and serve locally:
   ```bash
   python3 -m http.server 5500
