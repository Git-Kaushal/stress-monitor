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

## How it works

1. [MediaPipe Face Mesh](https://developers.google.com/mediapipe) runs in the browser (WebAssembly) and returns facial landmarks.
2. Three measures are taken from them: inner-brow gap, brow-to-eye distance and lip gap.
3. During calibration, the median and spread of each measure are recorded.
4. Afterwards, each frame is scored as a weighted z-score against that baseline. The median of the last 10 seconds is taken every 5 seconds and mapped to a level:

| Score | Level |
|---|---|
| below 0.8 | Calm |
| 0.8 to 1.6 | Some tension |
| 1.6 or above | High tension |

The thresholds (`BANDS`) and feature weights (`WEIGHTS`) are starting values. Tune them by testing with several people in different lighting, with and without glasses.

## Privacy

- Video frames and landmarks never leave the browser.
- The only thing stored is a list of `{time, score, level}` values in the user's own IndexedDB.
- The optional class meter sends only this, every 30 seconds, with no cookies:

```json
{ "classId": "c42", "bucket": 1712345670, "level": 1 }
```

- No name, user ID, video or landmarks are sent.

## Run locally

Browsers allow the camera only on HTTPS or `localhost`, so opening the file directly will not work.

```
python -m http.server 8000
```

Open `http://localhost:8000/` in Chrome or Edge.

## Test on a phone

Phones need HTTPS. The simplest route is GitHub Pages:

1. Push `index.html` to a public repository.
2. Go to **Settings > Pages**, choose **Deploy from a branch**, pick `main` and `/ (root)`, and save.
3. Open `https://YOURNAME.github.io/REPONAME/` on your phone.

You can also use a tunnel such as ngrok or cloudflared pointed at your local server.

## Configuration

Settings are in the `CFG` object near the top of the script.

| Option | Default | Meaning |
|---|---|---|
| `endpoint` | `""` | URL that receives class-meter updates. Empty keeps everything local. |
| `calibSeconds` | `25` | Length of the baseline period. |
| `sampleEveryMs` | `5000` | How often a value is stored. |
| `windowMs` | `10000` | Window of frames used for each value. |
| `reportEveryMs` | `30000` | Class-meter update interval. |
| `frameGapMs` | `125` | Minimum time between processed frames. Raise it on slow phones. |
| `maxPoints` | `120` | Number of samples shown on the chart. |

To use the class meter, set `endpoint` and open the page with a class link such as `?class=c42`.

## Server requirements for the class meter

If you receive the class-meter data, follow these rules:

- Show the class average only when at least 8 to 10 participants have reported in the same time bucket. With fewer, an "anonymous" average can point at one person.
- Do not log IP addresses or other identifiers next to the readings.
- Never link readings to names or accounts.
- Keep teacher monitoring self-view only. Do not show an individual teacher's readings to an employer.

## Before using with real users

- **Consent:** make it opt-in, explain what is measured, and give an easy way to switch it off.
- **Minors:** India's DPDP Act requires verifiable parental consent for children's data and restricts tracking of children. Check the rules where your users live.
- **EU users:** the EU AI Act prohibits emotion inference in schools and workplaces, with narrow exceptions. Get legal advice before offering it there.
- **Third-party hosting:** for production, download `face_mesh.js` and its model files and serve them from your own domain, then update the `locateFile` URL.

## Known limitations

- Talking, smiling, squinting at a bright screen and glasses can all raise or lower the score.
- Accuracy varies with lighting, camera quality and face angle.
- Only one face is tracked.
- Performance on older phones may be limited.

## Browser support

Current Chrome, Edge, Safari and Firefox, with a front camera.

## License

Add your chosen license here, for example MIT.
