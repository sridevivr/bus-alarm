# Bus Alarm

A lightweight web app that watches a WMATA bus route and sounds an alarm when your bus is almost at your stop. One HTML file, no install required.

## What it does

**Setup screen** — pick your route, search for your stop, and set how many minutes of warning you want before the bus arrives.

**Monitoring screen** — shows a live countdown to the next bus on your route, a colour-coded ring that fills as the bus gets closer (green → amber → red), and a list of every other bus arriving at the same stop.

**Alarm** — when the countdown hits your threshold, a full-screen alert fires, a repeating beep plays through the device speaker, and a browser notification pops up even if the tab is in the background. Tap "Got it" to dismiss.

## Setup

### 1. Get a WMATA API key

Create a free account at [developer.wmata.com](https://developer.wmata.com) and subscribe to the **Default Tier** (free, no credit card). Copy your primary or secondary key from the portal.

### 2. Paste the key into index.html

Open `index.html` in a text editor. Near the top of the `<script>` block, find:

```js
const WMATA_API_KEY = 'YOUR_WMATA_API_KEY_HERE';
```

Replace `YOUR_WMATA_API_KEY_HERE` with your key.

### 3. Deploy to GitHub Pages

1. Push `index.html` to a GitHub repository.
2. Go to **Settings → Pages**, set the source to the branch and root folder (`/`).
3. GitHub will publish the app at `https://<your-username>.github.io/<repo-name>/`.

Opening it over HTTPS is required for browser notifications to work.

**Local testing**: you can also just open `index.html` directly in a browser. The WMATA API works fine from a `file://` URL. Only notifications are disabled in that context.

## How to use it

### First time (setup)

1. Open the app.
2. Type your bus route (e.g. `D50`) in the **Bus Route** field.
3. Optionally type part of your stop name, then click **Find**. A scrollable list of stops appears — tap the right one to select it.
   - If you already know your stop ID, click **"I know my stop ID"** and enter it directly instead.
4. Drag the **Alert me** slider to how many minutes before arrival you want to be warned (2–20 min).
5. Tap **Start Watching**.

### Every day (monitoring)

The app goes straight to the monitoring screen. You'll see:

- The **route badge** and stop name in the top bar. Tap ⚙️ to go back to setup.
- A **large countdown** showing minutes until the next bus, with a ring that fills as it approaches.
- A **"Also at this stop"** list of other buses arriving soon.
- A **status line** at the bottom showing when predictions were last fetched and when the next refresh happens (every 60 seconds).

### When the alarm fires

A red full-screen overlay appears and a beep pattern plays. Tap **"Got it, I'm heading out!"** to dismiss it. The alarm won't fire again for the same bus — it resets automatically once that bus has passed and a new one is on the way.

## Notes

- **Weekend routes**: WMATA's route details API occasionally returns no schedule data on weekends for some routes. If stop search fails, use the stop ID fallback.
- **Stop IDs**: you can find a stop's ID on the [WMATA trip planner](https://wmata.com) or by inspecting a nearby stop's bus shelter sign. Stop IDs are 7-digit numbers.
- The app uses no cookies, no analytics, and sends no data anywhere except the WMATA API.
