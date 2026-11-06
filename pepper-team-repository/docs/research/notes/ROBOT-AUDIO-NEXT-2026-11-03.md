# Robot microphone milestone — November 3, 2026

## Fresh evidence
Native authenticated qi session to the head at <local-network-IP>:9503 succeeded and closed cleanly. Saved raw method/signal/property descriptions in research/audio-contract-2026-11-03.json. No capture, subscriptions, audio settings or robot actions were performed.

- ALAudioDevice is registered. Its public subscribe(string), unsubscribe(string) and setClientPreferences(string,int,int,int) interfaces are present.
- ALAudioRecorder is not registered; do not depend on that service.
- ALSpeechRecognition is registered. Presence alone does not prove unrestricted transcription or model integration.
- RoboHeartsSpeechReco is registered and exposes processRemote with four dynamic arguments. This proves an existing receiver service is present, not that it is actively subscribed or currently recording. Do not stop or reconfigure it.

## Selected next experiment
Build a uniquely named, short-lived receiver using the existing authenticated session, then request 16 kHz front-channel audio for that receiver only. The vendor Python example uses channel configuration 3 and processRemote(channel count, samples per channel, timestamp, input buffer): https://doc.aldebaran.com/2-5/dev/python/examples/audio/audio_soundprocessing.html . This is older documentation, so validate actual callback values and byte counts on this NAOqi 2.9 installation before claiming compatibility.

Prepare connection and registration before capture. The operator explicitly starts a short recording. Signal ready only after actual audio frames arrive, rather than when permission or subscription requests return. Use a bounded eight-second capture window, capped memory, monotonic timestamps and a unique subscriber name. Unsubscribe only that name and unregister only our receiver in finally; close the session on every outcome. No robot-side recording files, other-subscriber removal, global microphone changes or cloud uploads. If no callback arrives, stop and report that failure; do not silently switch microphones or reinterpret empty data.

Acceptance: local WAV playback includes “one, two, three; my favorite color is green” clearly from the beginning. Record channels, sample rate, sample count, expected/received bytes, time to first frame and callback gaps. Distinguish stream arrival from intelligibility and transcription. Keep captured audio local to the project and obtain the operator’s readiness before starting.

## Priority after audio
Bounded operator-requested base movement with a working stop path and observed result; separate this from floor-plan navigation. Display layout refinement and installation-log diagnosis remain open, but the physically demonstrated browser map avoids making those blockers for the microphone milestone. The Mac is an initial receiver/test host, not a decision that the final product requires this particular laptop.
