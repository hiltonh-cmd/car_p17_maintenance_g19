# Garage — Service History

A single-file HTML dashboard for tracking vehicle service records across multiple vehicles. Open `index.html` in any browser — no server or dependencies required.

---

## Adding a Service Record

Find the vehicle's `timeline` array in the `GARAGE` config at the top of the file and add an entry at the **top** of the array (most recent first).

```js
{ dot:'minor', date:'Jun 2026 · 88,000 mi', title:'Oil and Filter Changed', shop:'Christian Brothers Automotive', tags:['routine'] },
```

### `dot` options

| Value | Appearance | Use for |
|-------|-----------|---------|
| `'minor'` | Small hollow dot | Routine/minor services |
| `''` | Standard accent dot | Notable services |
| `'major'` | Large glowing dot | Significant repairs |
| `'s'` | Blue dot | Sierra vehicle (use instead of `''` for notable Sierra records) |

### `tags` — Unified Tag Taxonomy

The same 9 tags are used everywhere: timeline records, section filters, card colors, chart legend, and search. Every tag on a record must come from this list.

| Tag | Use for |
|-----|---------|
| `routine` | Oil changes, inspections, general maintenance |
| `engine` | Engine mechanical work — belts, pumps, air filters |
| `brake` | Brakes, rotors, brake fluid |
| `cooling` | Coolant, thermostat, water pump, radiator |
| `turbo` | Turbocharger and boost-related work |
| `electrical` | Battery, lighting, modules, sensors |
| `transmission` | Transmission, differential, transfer case |
| `fuel` | Fuel system, injectors, spark plugs |
| `tires` | Tires, wheels, alignment, rotation |

A record can have multiple tags if the work spans categories:
```js
tags: ['routine', 'tires']   // oil change that included a tire rotation
tags: ['engine', 'cooling']  // belt + coolant flush done together
```

Multi-tag records will appear in every matching section — this is intentional, as the work genuinely covers both categories.

---

## Updating Vehicle Info

After each service, update the `meta` block for the vehicle:

```js
meta: {
  latestService: 'Jun 2026',   // ← update after each service
  odometer: '88,000 mi',       // ← update after each service
  ...
}
```

Also update the `stats` array (record count, oil change count) and the `mileageProgress` bars in Overview.

---

## Updating the Charts

**Miles per year** — edit the `milesPerYear.data` array. The last entry is the current partial year; update `partialYearIndex` if you add a new year.

**Service event counts** — edit the `serviceChart.datasets` arrays. Each number corresponds to a year in `serviceChart.labels`. The dataset labels must match the unified tag labels (`'Oil & Routine'`, `'Brakes'`, etc.).

---

## Updating the Maintenance Schedule

Each vehicle has a `schedule` object with an `intro` string and a `sections` array. Each section has a `label` and an `items` array.

```js
schedule: {
  intro: "Brief description of the schedule source and current odometer.",
  sections: [
    {
      label: 'Near Term — Due Now or Overdue',
      items: [
        {
          mi: '90,000 mi',           // mileage interval or 'Annual' / 'Every 2 yrs'
          service: 'Oil & Filter Change',
          status: 'ok',              // 'ok' | 'upcoming' | 'due' | 'na'
          note: 'Last done Feb 2026 at 85,519 mi.'
        },
      ]
    },
  ]
}
```

### `status` options

| Value | Badge | Use when |
|-------|-------|---------|
| `'ok'` | On Track (green) | Recently done, within interval |
| `'upcoming'` | Due Soon (orange) | Approaching interval or mildly overdue |
| `'due'` | Overdue (red) | Past recommended interval |
| `'na'` | N/A (grey) | Not applicable to this vehicle |

Update the schedule after each service visit — change the relevant item's `status` to `'ok'` and update the `note` with the new mileage.

---

## Adding a New Vehicle

1. Add a new key to the `GARAGE` object, copying the structure of an existing vehicle.
2. Define `navSections` with `filterTags` for each category section you want. Only include sections relevant to that vehicle (e.g. no `turbo` section for the Sierra).
3. A tab will appear automatically — no other changes needed.

### `filterTags` on `navSections`

Each non-special section entry requires a `filterTags` array that controls which records appear in that section:

```js
{ id:'brakes', label:'Brakes', dot:'#5dba7d', count:'2', filterTags:['brake'] },
{ id:'engine', label:'Engine & Oil', dot:'#e08c55', count:'15', filterTags:['engine','routine'] },
```

`filterTags` is the single authority for section filtering — a record appears in a section if any of its tags match. The special sections (`overview`, `timeline`, `schedule`, `shops`) do not use `filterTags` and are rendered by built-in logic.

---

## File Structure

Everything lives in one file:

```
index.html
├── <style>          CSS — visual design and component styles
├── <script>         GARAGE config — all vehicle data, records, and schedules
├── <body>           Minimal shell (tabs, search bar, panel container)
└── <script>         Rendering engine, chart init, and search logic
```

The rendering engine reads entirely from the `GARAGE` config — no HTML needs to be edited manually to add records, vehicles, or schedule items.
