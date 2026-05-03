# Bus Alarm — project notes for Claude

## What this app is

A single-file web app that alerts a user when their bus is approaching a specific stop. The user picks a route and stop on a setup screen, sets how many minutes of warning they want, then watches a live countdown on a monitoring screen. When the bus hits the threshold, a full-screen alarm fires with audio and a browser notification.

## Tech stack

Plain HTML, CSS, and JavaScript in a single `index.html`. No frameworks, no build step, no dependencies beyond a Google Fonts stylesheet loaded from the CDN. Everything runs client-side in the browser.

## WMATA API endpoints

**Route Details**
```
GET https://api.wmata.com/Bus.svc/json/jRouteDetails?RouteID={route}&api_key={key}
```
Used on the setup screen when the user clicks "Find". Returns all stops for a given route across both directions. The app deduplicates stops by StopID and filters them by the user's text input.

**Next Bus Predictions**
```
GET https://api.wmata.com/NextBusService.svc/json/jPredictions?StopID={stopId}&api_key={key}
```
Used on the monitor screen, polled every 60 seconds. Returns all upcoming bus arrivals at a given stop. The app isolates the tracked route's soonest arrival for the countdown ring and lists everything else in the "Also at this stop" panel.

## API key

The key is hardcoded near the top of the `<script>` block in `index.html`:

```js
const WMATA_API_KEY = 'YOUR_WMATA_API_KEY_HERE';
```

Replace the placeholder with a real key from the [WMATA Developer Portal](https://developer.wmata.com). The free **Default Tier** is sufficient — it allows 10 requests/second and 50,000 requests/day, well above what this app needs.

## Testing locally

Open `index.html` directly in a browser:

```
open index.html          # macOS
start index.html         # Windows
xdg-open index.html      # Linux
```

No server required. The WMATA API accepts requests from `file://` origins. Browser notifications require a secure context (HTTPS or localhost), so they won't fire when opening the file directly — use a local server or GitHub Pages to test those.

## Known quirks

**Weekend schedule gap**: The Route Details endpoint (`jRouteDetails`) sometimes returns `"No schedule data available"` on weekends for certain routes. This only affects the stop-search flow on the setup screen. Users who already know their stop ID can use the "I know my stop ID" fallback at the bottom of the stop field to bypass it entirely.

**AudioContext autoplay policy**: The `AudioContext` is created inside the "Start Watching" button click handler to satisfy browser autoplay requirements. If the alarm ever fails to beep, it's usually because the context was created outside a user gesture — keep initialization inside `enterMonitor()`.

**WMATA prediction rounding**: The `Minutes` field in prediction responses is an integer rounded to the nearest minute. A value of `0` means the bus is boarding or about to depart, not that it's exactly on time.
