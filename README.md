# Facial Tension Check

A private, in-browser indicator of facial tension for students and teachers. It reads face geometry from the webcam, compares it with the person's own relaxed baseline, and shows how their tension changes over time. Everything runs on the user's device. No video is uploaded or stored.

> **This is a self-reflection tool, not a diagnosis.** Facial geometry is a weak signal for stress. Concentration, lighting, glasses and talking all change it. Do not use it for grading, evaluation, discipline or any decision about a person.

## Features

- **Personal calibration:** the first 25 seconds set each person's own baseline, so scores are relative to them, not to a fixed number.
- **Size-independent measures:** distances are divided by eye-to-eye distance, so moving closer to or further from the camera does not change the score.
- **Rolling timeline:** one value every 5 seconds, drawn as a 10-minute chart with calm, some-tension and high-tension bands.
- **Local history:** samples are saved in the browser's IndexedDB. A "Delete my history" button clears them.
- **Break suggestion:** shown when tension stays high for about 20 seconds.
- **Optional anonymous class meter:** off by default and opt-in per person. Sends only a level from 0 to 2.

## Known limitations

- Talking, smiling, squinting at a bright screen and glasses can all raise or lower the score.
- Accuracy varies with lighting, camera quality and face angle.
- Only one face is tracked.
- Performance on older phones may be limited.

## Browser support

Current Chrome, Edge, Safari and Firefox, with a front camera.

## Run locally

Browsers allow the camera only on HTTPS or `localhost`, so opening the file directly will not work.

```
python -m http.server 8000
```

Open `http://localhost:8000/stress-monitor.html` in Chrome or Edge.

## Test on a phone

Phones need HTTPS. The simplest route is GitHub Pages:
 Open `https://git-kaushal.github.io/stress-monitor/` on your phone.

You can also use a tunnel such as ngrok or cloudflared pointed at your local server.

Licencse: Released under the MIT License Copyright (c) 2026 Kaushal J
