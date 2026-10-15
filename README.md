# Mind's Eye // Gaze & Focus Trainer

An open-source, zero-hardware gaze & focus trainer that turns an ordinary device
camera into real-time gaze input and guided stillness sessions. No EEG headband,
no health-data permissions, no App Store health review — just computer vision,
running entirely on-device in the browser.

This is a pivot from the original "meditation-hardware-as-gamepad" concept:
instead of reading brain data (which mobile app stores gatekeep hard), it reads
*where you look* and *how steady you hold it* — and that turns out to be the
better game controller anyway.

## What it does

- **Gaze cursor** — iris tracking estimates where you're looking on screen.
  Calibrate once (4 corner dots, ~6 seconds) and the cursor follows your eyes.
- **Blink = click** — a detected blink fires a click at your gaze point.
  Hands-free input with zero health data involved.
- **Meditation mode** — breath pacer ring, stillness-driven levitating orb,
  and timed sessions (3 / 5 / 10 min) with an end-of-session summary.
- **Simulation mode** — synthetic gaze/blink feed for testing without a camera.
- **On-device only** — MediaPipe Tasks Vision (FaceLandmarker) runs locally;
  no video or face data ever leaves the page.

## Metrics (honest labels)

- **Gaze point** — estimated from iris-center offset vs. eye corners, averaged
  across both eyes, smoothed, mapped through your calibration.
- **Stillness** — a simple heuristic: low gaze velocity + low head movement +
  eyes open. It measures *steadiness for interaction*, not attention or cognition.
- **Blinks/min** — rolling 60-second count from eye-aspect-ratio detection.

Mind's Eye is a focus-training toy, **not a medical device**. Nothing here
measures attention, cognitive load, or health.

## Run it (GitHub Pages, ~60 seconds)

1. Push this repository to your GitHub account.
2. **Settings → Pages** → Deploy from a branch → `main`, folder `/ (root)`.
3. Open the URL on your phone or desktop (HTTPS is required for camera access).

Or open `index.html` from any static host. Camera needs HTTPS or localhost.

## How gaze estimation works

MediaPipe's FaceLandmarker returns 478 facial landmarks including iris centers
(468 left, 473 right). For each eye, the iris center's offset from the eye-box
center — normalized by eye width/height — gives a raw gaze vector. Left and
right eyes are averaged, smoothed with an exponential moving average, then
mapped to screen coordinates through a 4-point calibration (raw extremes at
the screen corners → linear map, clamped 0–1).

Blink detection uses the classic eye-aspect-ratio (EAR) with hysteresis
(close below 0.19, reopen above 0.23) to avoid flutter.

## Stack

- Single `index.html`, no build step, no dependencies to install.
- `@mediapipe/tasks-vision` (FaceLandmarker) via CDN, GPU delegate.
- MIT licensed.
