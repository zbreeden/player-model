# FourTwenty • The Player (Sim Edition)

> Probability & decision simulator — scenarios, bankroll paths, and outcomes under uncertainty.

The Player has shifted from primarily story-driven CYOA to a **probability/scenario simulator**. You can still keep lightweight **Story Mode** for narrative demos, but the focus is now **Sim Mode**: define scenarios (odds + payouts), auto‑run Monte Carlo rounds, and analyze results in GA4/Power BI.

---

## What’s inside

* **Sim Mode** – scenario dropdown + **Auto‑Run** (Monte Carlo), bankroll HUD, CSV export.
* **Story Mode (optional)** – YAML storylets for quick narrative prototypes.
* **Telemetry** – unified `track()` → GTM → GA4 (custom events/params).
* **Artifacts** – `/artifacts/logs/*.csv` for analysis; optional PBIX in `/powerbi/`.

---

## Quick start

1. Serve locally (or GitHub Pages):

   ```bash
   python3 -m http.server 5500
   ```

   Open `http://localhost:5500/` and load `index.html`.
2. Add/adjust **scenarios** in `/seeds/scenarios.yml` (see below).
3. Pick a scenario → **Auto‑Run** → watch the log + GA4 **DebugView**.
4. **Export CSV** → feed to Power BI (or your tool of choice).

---

## Seeds

### 1) `/seeds/scenarios.yml` (Sim Mode)

Each scenario specifies a bankroll, a **bet policy**, and outcome probabilities with net **multipliers** relative to the stake (e.g., `-1` lose stake; `+1` even money; `+35` roulette straight‑up):

```yaml
- id: coinflip_even
  label: "Coin Flip (even money)"
  bankroll: { start: 100, goal: 200, ruin_at: 0 }
  bet_policy: { kind: fixed_fraction, fraction: 0.05, min: 1 }
  rounds: 200
  outcomes:
    - id: win  ; p: 0.5 ; multiplier:  1
    - id: lose ; p: 0.5 ; multiplier: -1
```

Supported bet policies: `fixed_fraction`, `fixed_amount`, `kelly` (with optional `cap`, `p`, `b`).

### 2) `/seeds/storylets.yml` (Optional Story Mode)

Simple nodes with `choices`, `effects`, and `reward`. Runs side‑by‑side with Sim Mode for demos.

---

## Event taxonomy (GA4 via GTM)

**Sim events**

* `bet_placed` `{scenario_id, round, bet, bankroll}`
* `round_complete` `{scenario_id, round, outcome_id, multiplier, bet, profit, bankroll}`
* `ruin` `{scenario_id, round, bankroll}`
* `goal_hit` `{scenario_id, round, bankroll}`
* `run_start` / `run_complete` `{scenario_id, final_bankroll?, total_reward?}`
* `export_csv` `{name, rows}`
* `sim_param_changed` `{param, value}`

**Story events (optional)**

* `choice_made` `{node_id, choice_id, reward}`
* `state_updated` `{delta.*}`

**Context sent on every hit**: `src="player"`, `app`, `version`, `session_id`, `env`, `run_id`, `world_name`, `mode`, `branch`.

---

## GTM/GA4 mapping (short)

* **GTM**: One GA4 Event tag (Event Name = `{{Event}}`) with trigger ***Player — Any Event*** where `src = player`.
* **Event parameters to map** (rows in the GA4 tag):
  `world_name, mode, branch, scenario_id, round, bet, profit, bankroll, outcome_id, multiplier, final_bankroll, total_reward, node_id, choice_id, tick, reward, delta_supplies, delta_morale`.
* **GA4 Custom definitions**

  * *Dimensions*: `world_name`, `mode`, `branch`, `scenario_id`, `outcome_id`, `node_id`, `choice_id`.
  * *Metrics*: `round`, `bet`, `profit`, `bankroll`, `final_bankroll`, `tick`, `reward`, `total_reward` (optional: `delta_supplies`, `delta_morale`).
  * Avoid registering `run_id` (high cardinality).
* Mark **Key events**: `goal_hit`, `ruin`, `export_csv` (optional: `run_complete`).

---

## Power BI / Analysis ideas

* **EV & Variance** per scenario: avg profit/round, SD, Sharpe‑like ratio.
* **Ruin probability** and **time‑to‑goal** distribution.
* **Bankroll path** (median with p5/p95 bands) across rounds.
* **Policy comparisons**: fixed fraction vs Kelly vs flat.

---

## Project structure

```
player-model/
  index.html                # Sim + (optional) Story UI
  /seeds/                   # scenarios.yml, (storylets.yml), world.yml, ...
  /artifacts/
    /logs/                  # exported CSVs
    /screens/               # screenshots/GIFs
  /powerbi/                 # PlayerScorecard.pbix (optional)
  /gtm/                     # docs for event & parameter mapping
```

---

## Changelog

* **v0.2.0 – Sim Edition**: Added Scenarios, Auto‑Run, bankroll metrics, new sim events.
* **v0.1.x – Starter**: Narrative storylets, basic telemetry, CSV export.

---

## License

MIT © FourTwenty Analytics

[← Back to FourTwenty](https://zbreeden.github.io/FourTwentyAnalytics/)
