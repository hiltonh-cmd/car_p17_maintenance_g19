# Garage — Service History

A single-file HTML dashboard for tracking vehicle service records. Open `garage-service-history.html` in any browser — no server or dependencies required.

---

## Adding a Service Record

Find the vehicle's `timeline` array in the `GARAGE` config at the top of the script and add an entry. New records should go at the **top** of the array (most recent first).

```js
{ dot:'minor', date:'Jun 2026 · 88,000 mi', title:'Oil and Filter Changed', shop:'Christian Brothers Automotive', tags:['routine'] },
```

**`dot` options**
| Value | Appearance |
|-------|-----------|
| `'minor'` | Small hollow dot — routine/minor services |
| `''` | Standard accent dot — notable services |
| `'major'` | Large glowing dot — significant repairs |
| `'sierra'` | Blue dot — use on the Sierra vehicle |

**`tags` options**
`routine` · `engine` · `brake` · `cooling` · `electrical` · `turbo` · `major` · `transmission` · `fuel` · `tires` · `inspection` · `accessories`

---

## Updating Vehicle Info

Edit the `meta` block for the vehicle:

```js
meta: {
  firstService: 'Jan 2017',
  latestService: 'Mar 2026',  // ← update after each service
  odometer: '71,774 mi',      // ← update after each service
  ...
}
```

Also update the `stats` array (service record count, oil change count, etc.) and the `mileageProgress` bars in Overview.

---

## Updating the Charts

**Miles per year** — edit the `milesPerYear.data` array. The last entry is the current partial year; update `partialYearIndex` if you add a new year.

**Service event counts** — edit the `serviceChart.datasets` arrays. Each number corresponds to a year in `serviceChart.labels`.

---

## Adding a New Vehicle

1. Add a new key to the `GARAGE` object, copying the structure of an existing vehicle.
2. A tab for it will appear automatically — no other changes needed.

---

## File Structure

Everything lives in one file. The layout at a glance:

```
garage-service-history.html
├── <style>          CSS — visual design
├── <script>         GARAGE config — all data lives here
├── <body>           Minimal shell (tabs, search, panel container)
└── <script>         Rendering engine + chart + search logic
```
