# Space Mission Control

Three self-contained, browser-only astronomy and mission-design simulators, styled as one
mission console. No frameworks, no build step, no dependencies, no network calls — open a file
and it runs.

**[Solar System Explorer](solar-system.html)** · **[Orbital Dynamics Lab](orbital-lab.html)** ·
**[Mission Planner](mission-planner.html)**

Each page is a single `.html` file with its CSS and JavaScript inline. They link to each other
through the shared top navigation, so the folder behaves like a small site with exactly three
files.

---

## Quick start

Open any page directly:

```bash
# Windows
start solar-system.html

# macOS
open solar-system.html

# Linux
xdg-open solar-system.html
```

Or serve the folder if you prefer a real origin (recommended if your browser restricts
`file://` features such as clipboard access):

```bash
python3 -m http.server 8000
# then visit http://localhost:8000/solar-system.html
```

Modern versions of Chrome, Edge, Firefox and Safari are supported. Everything works offline.

---

## The pages

| Page | What it does | Core controls |
| --- | --- | --- |
| [`solar-system.html`](solar-system.html) | Top-down ecliptic view of the eight planets with a dated satellite count index, per-planet data cards, search, and a two-planet comparison mode. | Time speed and play/pause, planet index search, comparison pair selectors, reset |
| [`orbital-lab.html`](orbital-lab.html) | Earth-centred prograde orbit with live two-body telemetry, reference-orbit presets, and a side-by-side comparison of two configurations. | Orbital altitude, eccentricity, reference orbit preset, simulation speed, play/pause, units, restore, reset |
| [`mission-planner.html`](mission-planner.html) | Illustrative mission design for five destinations: configure a spacecraft, see which constraints fail and why, rank profiles against weighted criteria, and export a briefing. | Destination tabs, spacecraft class, payload/mass/budget/duration sliders, risk tolerance, priority presets, four ranking weights, evaluate, save/load, export |

### Solar System Explorer

Planets are drawn from real orbital elements but with deliberately compressed scales so that
Mercury and Neptune can share a screen: radii are mapped logarithmically (0.39–30.07 AU),
diameters use a compressed power scale, and angular speeds follow
`T′ = 365.26 d × (T / 365.26 d)^0.55` — real period *ordering* is preserved while inner planets
are sped up and outer planets slowed so they stay legible. The page says so explicitly rather
than implying the orbits are to scale.

### Orbital Dynamics Lab

Two-body circular and elliptical motion: the orbit is integrated and the telemetry panel reports
altitude, radius, speed, true anomaly, period, perigee and apogee from the current altitude and
eccentricity. Presets cover LEO, GPS-type MEO, geostationary, and
Molniya-type orbits. Perturbations, drag and third-body effects are out of scope, and the
method panel states that.

### Mission Planner

The largest of the three. For each of LEO, GEO, the Moon, Mars and Jupiter it separates three
kinds of information on the page itself:

- **Established facts** — the real physical setting (delta-v scale, environment, light-time).
- **General principles** — how missions to that destination are usually conceived.
- **Illustrative assumptions** — a planning profile: payload capacity, cost basis, duration
  range, complexity, and supported spacecraft classes. These are teaching numbers, not quotes.

Evaluation reports one of three states — *Feasible under the stated assumptions*, *Feasible with
constraints*, or *Infeasible under the stated assumptions* — with reasons drawn from a rule
table (payload over capacity, budget below the profile basis, duration outside the supported
range, spacecraft class incompatible with the destination, mass outside the profile range).
Ranking is a weighted sum of four criteria normalised to 0–1 (cost, duration, scientific
capability, risk), with the arithmetic printed under the table so the order is never opaque.
The briefing generator renders the current configuration into HTML and into a downloadable
plain-text report.

---

## How it is built

- **One file per page.** HTML, CSS and JS are inline; there are no imports, no `<script src>`,
  no fonts, no images and no URLs of any kind in the three pages. Nothing is fetched at runtime,
  which is also why the pages work from `file://` without a server.
- **Shared design language.** CSS custom properties in `:root` define the palette; the topbar,
  nav, panels, badges, buttons, chips and stat rows are the same components on all three pages,
  so they look like one console. Light and dark themes are both supported; the choice is
  remembered in `localStorage`, and the first visit starts in dark.
- **Charts are hand-drawn.** Every chart is `<canvas>` with 1× device-pixel-ratio scaling and
  redraw-on-resize, or plain CSS. No charting library is involved, which is why `resize` and
  theme changes re-render the plots rather than restyling an existing chart.
- **State is local.** Preferences and saved configurations live in `localStorage` under:
  - `smc-theme` — theme choice, shared by all three pages
  - `smc.missionPlanner.config.v1` — saved mission configuration (destination, spacecraft,
    masses, budget, duration, risk, priority and the four ranking weights)
  Every read and write is wrapped so that private-browsing modes and disabled storage degrade
  to defaults instead of throwing.
- **Accessibility.** Semantic landmarks, a skip link, labelled inputs, `aria-live` status
  regions for evaluation and briefing updates, `aria-pressed` on toggles, visible focus rings,
  keyboard-operable canvas alternatives (numeric readouts), and a `prefers-reduced-motion`
  path that stops the animation loops.

### File layout

```
Space Mission Control/
├── solar-system.html     # page 1
├── orbital-lab.html      # page 2
├── mission-planner.html  # page 3
├── README.md
└── .gitignore
```

---

## Accuracy and limitations

These are **educational decision-support toys**, not engineering tools, and the pages say so in
their own method sections. Specifically:

- Nothing here validates a spacecraft design, a launch opportunity, or a mission architecture.
- The Mission Planner does not model propulsion, trajectories, launch vehicles, launch windows,
  power or thermal budgets, reliability, or programme reserves. Its cost and duration figures
  are illustrative planning assumptions chosen to be plausible in order of magnitude.
- Payload capacities, cost bases and duration ranges are per-profile teaching values; they are
  not quotations from any agency, contractor or mission.
- The Solar System Explorer's orbital radii and body sizes are compressed for legibility, and
  its satellite counts are a dated snapshot (mid-2026) that changes as discoveries are confirmed.
- The Orbital Dynamics Lab solves the idealised two-body problem only.
- Ranking scores are relative fits on a 0–1 scale, not probabilities of success.

Real mission design depends on all of the above and more. Treat these pages as a way to build
intuition about trade-offs — cost against duration, payload against spacecraft class, ambition
against budget — not as a source of numbers to quote.

---

## Contributing

The pages are dependency-free and hand-editable, so changes are a matter of opening the file.
If you add a feature, keep the three constraints that make the suite useful:

1. **Stay inline and single-file** — no build step, no CDN, no external asset.
2. **Keep the illustrative/measured distinction explicit** in the copy. If a number is a
   teaching assumption, label it as one.
3. **Keep the shared design tokens** so the console still looks like one product.

Please check a change in a browser at both desktop and narrow (≈375 px) widths, in both themes,
and confirm the console is free of errors before opening a pull request.

## License

No license has been chosen yet. Until one is added, the default copyright applies and others
may not reuse the code — see [Choose a license](https://choosealicense.com/) if you want to
open it up.
