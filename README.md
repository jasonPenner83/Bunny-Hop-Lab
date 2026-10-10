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

## Overlay, reference and key frames

- Layer toggles (Video, Skeleton, Reference) are remembered per device.
- Reference: a synthetic rider with every metric on target, time-aligned to your run
  phase by phase and pinned to your pedals, scaled to your leg length.
- Key frames: one JPEG per phase plus any metric moment, captured with the current
  layers and a caption band. Save one, or Save all through the share sheet.
- Rider tracking: up to two poses per frame; the rider is followed by continuity from the
  clearest detection, and only the longest stretch where the hips, knees, ankles and
  shoulders are visible and inside the frame is analyzed.

## Full screen, casting and on-screen angles

- The expand button next to the timer opens a full-screen theatre view (true full screen
  and landscape lock where the browser allows; on iPhone the installed app already hides
  the browser bars). Controls fade while playing; tap to bring them back. Esc, Space and
  arrow keys work on a laptop.
- Casting is screen mirroring (AirPlay / Android Cast / Chrome Cast tab), because the
  overlay is drawn on the phone. Cast to TV shows the steps.
- Angles layer: knee, hip, elbow and torso (from horizontal) for you vs the reference
  rider at the matched moment, with the difference coloured (within 8° green, 18° amber,
  else red), plus the current phase's metrics. It is drawn into the canvas, so it shows on
  a mirrored TV and in key frames.

## Adjust mode

- Tap Adjust under the replay. Scrub to a frame and tap Set here on a phase to pin it;
  Auto returns it to detection. Later phases are re-detected from pinned ones, and
  metrics, reference timing and key frames update.
- Drag any joint (head, shoulder, elbow, wrist, hip, knee, ankle) to fix a bad track.
  The correction blends into the 4 frames on each side, and two nearby fixes on the
  same joint interpolate. Undo last fix / Clear fixes.
- Edits belong to the loaded clip (phase pins per skill) and reset when a new clip loads.

## Files

index.html (whole app), manifest.webmanifest, sw.js (offline cache), icons.
