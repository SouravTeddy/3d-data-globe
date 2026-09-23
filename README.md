# 3D Data Globe

A standalone, single-file 3D visualization for device/user activity data. Each device is a glowing blob floating in 3D space, sized by activity. Clicking a blob blooms small satellite nodes around it, one per recent event, and opens a detail panel with that device's activity log.

Built on [3d-force-graph](https://github.com/vasturiano/3d-force-graph) + [Three.js](https://threejs.org/). No build step, no dependencies to install — it's one HTML file.

## Running it

Just open `index.html` in a browser, or serve it locally:

```bash
python3 -m http.server 8000
```

Then visit `http://localhost:8000`.

It ships with synthetic demo data (`DUMMY_USERS` in the script) so it runs immediately with zero setup.

## Wiring in your own data

The visualization expects data shaped like this — one object per device:

```js
{
  id: 'device_123',
  points: 42,
  platform: 'ios' | 'android' | 'web',
  city: 'Some City',
  totalDurationMs: 1234567,       // first-seen -> last-seen span, in ms
  size: 8.5,                       // rendered blob radius
  color: '#9fd8f5',                 // rendered blob color
  inactive: false,                  // true = rendered as a dim, small "no activity" blob
  events: [
    { type: 'session_start', time: new Date(...), detail: 'App opened' },
    // ...
  ]
}
```

To go live:

1. Replace the `DUMMY_USERS` array (search for `const DUMMY_USERS`) with a fetch against your own backend or database, resolving to an array in the shape above.
2. Set `baseUsers` to the result before `initPresentation()` runs.
3. **Never call a database directly from the browser with an admin/service key.** If you're using something like Supabase, either:
   - Use a key scoped to read-only public data (e.g. Supabase's `anon` key with row-level security policies), or
   - Fetch through your own backend endpoint that holds the real credentials server-side and returns only the fields you want public.

The `EVENT_META` object (search for `const EVENT_META`) maps each `event_type` to a label and color for the satellite blobs and detail log — add or rename entries to match your own event vocabulary.

## Removing the "support this project" card

There's a small card in the top-left corner with links to support the original developer. To remove it:

1. Delete the `<div class="card" id="support-card">...</div>` block in the HTML (around line 70).
2. Delete the associated CSS rules, all prefixed `#support-card` (around lines 52-67).

## License

No license file is included — add one if you plan to redistribute this.
