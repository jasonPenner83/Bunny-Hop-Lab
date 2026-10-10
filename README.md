# Bunny Hop Lab

Side-on mountain bike skill analysis (bunny hop, kicker jump) that runs entirely on the phone. MediaPipe Pose Landmarker
(full model) tracks the rider frame by frame; the app finds the hop phases, measures
joint angles and heights, and names the earliest fault as the top fix.

## Hosting

Served by GitHub Pages from the `main` branch root. Every push to `main` redeploys in about a minute.
Bump `VERSION` in `sw.js` when you change files so installed phones pick up the update.

Any other static HTTPS host also works (`firebase.json` is included for Firebase Hosting).

## On the phone

Open the URL in Safari or Chrome, then Share > Add to Home Screen (iOS) or Install app
(Android). The first clip you analyze downloads the pose model (~9 MB); after that it is
cached and works with no signal.

## How the numbers are made

- Frames are sampled at 30 per second of clip time, up to 15 s. Set "Clip speed" for
  slow-mo files so timings come out in real seconds.
- The camera-side limbs are used (auto-detected by landmark visibility, or set manually).
- Heights are in leg lengths (hip to ankle), converted to cm with leg = 0.48 x rider height.
- Phases: preload = lowest hip before rear lift; front lift = hands up 0.15 leg lengths;
  hips back = most rearward hip vs. ankle; leg push = max knee extension; rear lift = feet
  cross half their peak height; landing = feet back near baseline; absorb = deepest knee
  in the 0.5 s after landing.
- Kicker jump: takeoff = peak upward foot speed (the lip); landing = sharpest stop in
  downward foot speed after the peak; compress/pop = min/max knee angle around takeoff;
  bike pitch is estimated from the pedal-to-bar line vs. the approach. Airtime is checked
  against gravity to flag a wrong Clip speed setting.
- Targets are coaching ranges, tune them in CUES / analyze() in index.html.

## Files

index.html (whole app), manifest.webmanifest, sw.js (offline cache), icons.
