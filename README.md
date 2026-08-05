# Sommer 26

A family holiday planner. Week grid or day list, free days shown as a continuous soft band, live weather that follows wherever you are that day, and an ideas list you can drag onto the calendar.

Everything is editable in the browser. No build step, no dependencies, no backend.

## Files

```
index.html              the whole app
manifest.webmanifest    makes it installable
sw.js                   offline cache
icon-180.png            iOS home screen
icon-192.png            Android / browser
icon-512.png            splash + store size
icon-maskable-512.png   Android adaptive icon
```

## Put it on GitHub Pages

1. Create a repo — `sommer26` works.
2. Upload all the files above into the repo root (drag them into the GitHub web uploader; no command line needed).
3. **Settings → Pages → Source: Deploy from a branch**, branch `main`, folder `/ (root)`. Save.
4. Wait a minute. The site appears at `https://<username>.github.io/sommer26/`.

It has to be **https** for the offline cache to switch on. GitHub Pages is https by default, so that takes care of itself.

## Add it to the iPhone home screen

1. Open the Pages URL **in Safari** (Chrome on iOS can't install web apps).
2. Tap the **Share** button, then **Add to Home Screen**.
3. Name it and tap **Add**.

It then opens full screen with no browser chrome, keeps working without signal, and shows the `26` icon.

**Updating it later:** push a new `index.html`, then bump `CACHE` in `sw.js` (currently `sommer26-v3`). Without the bump, phones may keep serving the cached copy. `plan.json` is never cached, so plan changes always come through live.

## Syncing your devices, sharing with everyone else

One editor, many readers — using the repo itself as the store. No extra service.

**`plan.json` in this repo is the live plan.** Anyone opening the Pages URL fetches it and sees your current state. That's the family link: nothing to install, nothing to sign into, always up to date.

**Your own devices write back to it.** In *Einstellungen* there's a **Sync-Token** field. Paste a fine-grained PAT there — scoped to this repo only, Contents: read/write — and that device commits `plan.json` about two seconds after each change, and pulls the newest version when you open the app. Do this on your phone and your laptop and they stay in step.

Set it up once per device:

1. GitHub → Settings → Developer settings → **Fine-grained tokens** → Generate new token.
2. Repository access: **Only select repositories** → `sommer26`. Permissions: **Contents: Read and write**. Nothing else.
3. Open the app on that device → Einstellungen → paste into Sync-Token.

**The token is stored in that browser's localStorage and never leaves it** — it isn't in `index.html`, isn't in exports, and isn't in shared links. But it is a write credential sitting in a browser, so: scope it to this one repo, give it an expiry, and revoke it if you lose the device. Worst case someone edits a holiday planner.

A reader who never enters a token simply can't write. Their local edits stay on their machine; when you publish a newer plan they get a banner offering it.

### Other ways to move a plan

| | What it does |
|---|---|
| **Link kopieren** | Packs the whole plan into a URL. A frozen snapshot — good for "here's the plan as of today". |
| **Export / Import** | The same data as a JSON file. |
| **Edit `seed()`** | The fallback plan for someone with no `plan.json` and no local copy. Bump `SEED_VERSION` when you change it. |

Devices compare a **signature of the plan's content**, not clocks. Switching view or opening a day is not an edit, so it can't make a device wrongly believe it's ahead. On opening, a device that hasn't changed anything takes the published version silently; one that has diverged gets a banner and chooses. The app also re-checks whenever it comes back to the foreground, so bringing the phone out of your pocket is enough to pull the latest.

For several people editing at once you'd still want real merging. This resolves whole-plan conflicts by asking, which is right for one editor and thin for a family.

## Weather

[Open-Meteo](https://open-meteo.com) — free, no API key, no account. It blends the national weather services' models and picks the highest-resolution one for each location; for Austria that means DWD ICON-D2 and GeoSphere Austria at roughly 1–2 km, which is about as good as a consumer forecast gets here.

Two limits worth knowing:

- The forecast reaches **16 days out**. Days beyond that show `—` and fill in as they come into range.
- Each day shows **rain probability** as a coloured chip with a three-bar duration meter: one bar under 2 hours, two up to 6, three beyond. Probability tells you whether it rains; the bars tell you whether it rains all day. 90 % for one hour and 90 % for nine look nothing alike.
- The chip colour is a verdict, not a restatement of the number: grey under 25 %, mint to 60 %, solid teal for a day worth staying in. A near-certain but brief shower stays mint — high odds of getting wet for twenty minutes is not an indoor day.
- The day sheet spells out probability, millimetres and hours in words.
- Each day's forecast is fetched for **that day's location** — destination if you're travelling, otherwise the place, otherwise the last place carried forward. Type a location the app doesn't know and it geocodes it automatically on save.

## Editing

- **Tap a day** — status toggles (Frei / TA / ZA / Büro / Offen), Wo, Weiter nach, Was, and who's there. `‹ ›` or the arrow keys move to the next day without closing.
- **Drag an idea** by its grip onto any day. Drag the note back out of the day onto the ideas panel to return it. Dragging a note from one day to another moves it.
- **A day holds a list of entries, not one string.** Each has its own ✕, so an entry added by mistake can be removed on its own. Entries can span days with the −/+ control: a 3-day entry appears on all three, shows *Tag 2 von 3*, and removing or moving it acts on the whole run.
- **Locations** come from a ▾ picker that always lists every known place. The list fills itself: type any name into *Wo* or *Weiter nach* and it is geocoded and added. Manage them under Einstellungen → Orte.
- **On a travel day the weather is the destination's** — *Weiter nach* wins over *Wo*, since that is where you end up. The day sheet marks it *(Ziel)*.
- **Orte** is a third view: every location's forecast side by side, one row per place, one column per day. Steyr, Brunnbach, Wien and Graz are pinned by default and always shown; anywhere the plan takes you appears automatically, dimmed on days you aren't there. Pin or unpin from the row header or Einstellungen → Orte. The outlined cell in each column is where you're actually planned that day.
- **Pull down** at the top of the page to force a sync.
- **Assign without dragging.** The day sheet lists the ideas that suit that day's forecast, and *Alle Ideen* expands to the rest. Tap one and it lands on that day. This is the practical route on a phone, where dragging across a 21-row list is awkward.
- **Each idea carries a weather kind** — ☀ outdoor, ☂ indoor, • any — toggled by clicking the circle. The day sheet then offers the ones that suit that day's forecast under *Passt zu diesem Wetter*; tap one to assign it.
- **Title, subtitle and ideas** are edited in place — click and type.
- **Einstellungen** — trip dates, default location, and the people list.

## Legend

`TA` Telearbeit · `ZA` Zeitausgleich · `Büro` · `offen` not yet fixed · green band = a run of free days

## Design

Plus Jakarta Sans, warm off-white ground, soft-shadowed white cards, muted sage and coral accents. Free-day runs render as one continuous soft band with rounded ends — the same visual grammar as a date-range picker. In week view the location sits at the foot of each tile, so the bottom edge of the grid reads as an itinerary.
