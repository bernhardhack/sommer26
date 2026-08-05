# Sommer 26

A family holiday planner. Week grid or day list, free days marked with a highlighter band, live weather that follows wherever you are that day, and an ideas list you can drag onto the calendar.

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

**Updating it later:** push a new `index.html` to GitHub, then bump `CACHE = "sommer26-v1"` to `v2` in `sw.js` and push that too. Without the bump, phones may keep serving the cached copy.

## How the data works

The plan lives in the browser's local storage on each device — it is not synced. To move it:

**Einstellungen → Export** writes a JSON file. **Import** reads it back on another device. **Zurücksetzen** restores the original plan.

If you'd rather the plan travel with the file itself, export the JSON and paste it into the `seed()` function near the top of the `<script>` block in `index.html`. Then anyone opening the link sees the current plan, and local edits layer on top of it.

## Weather

[Open-Meteo](https://open-meteo.com) — free, no API key, no account. It blends the national weather services' models and picks the highest-resolution one for each location; for Austria that means DWD ICON-D2 and GeoSphere Austria at roughly 1–2 km, which is about as good as a consumer forecast gets here.

Two limits worth knowing:

- The forecast reaches **16 days out**. Days beyond that show `—` and fill in as they come into range.
- Each day's forecast is fetched for **that day's location** — destination if you're travelling, otherwise the place, otherwise the last place carried forward. Type a location the app doesn't know and it geocodes it automatically on save.

## Editing

- **Tap a day** — status toggles (Frei / TA / ZA / Büro / Offen), Wo, Weiter nach, Was, and who's there. `‹ ›` or the arrow keys move to the next day without closing.
- **Drag an idea** by its grip onto any day. Drag the note back out of the day onto the ideas panel to return it. Dragging a note from one day to another moves it.
- **Title, subtitle and ideas** are edited in place — click and type.
- **Einstellungen** — trip dates, default location, and the people list.

## Legend

`TA` Telearbeit · `ZA` Zeitausgleich · `Büro` · `Offen` not yet fixed · green band = a run of free days
