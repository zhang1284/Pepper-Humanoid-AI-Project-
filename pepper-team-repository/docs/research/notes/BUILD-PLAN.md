# Standalone Pepper application: initial build plan

November 2, 2026. Architecture and acceptance plan; no new application implementation is claimed yet.

## First milestone
A reliable conversation-and-vision app with explicit connection, recording, transcription, thinking, speaking and failure states. Notebook cells are not part of the normal user flow.

## Components
- Operator UI: connection health, selected provider, microphone choice, clear recording cue, local playback, camera preview, cancel, and action status.
- Audio input: native capture first, including recorder-start confirmation and a tested short startup buffer/countdown; do not claim readiness from an audio-context callback alone. Ensure captured speech includes the first word before removing playback diagnostics.
- Speech recognition adapter: independent transcript, with local whisper.cpp as a candidate. The language model receives recognized text; speech recognition can be evaluated separately from answer quality.
- Model adapter: common request/response contract for text and optional image; Gemini baseline, local candidate, fake provider. Provider failure cannot block robot control indefinitely. Cloud fallback must not upload data unexpectedly when local-only operation was selected.
- Pepper adapter: use the reviewed loopback bridge for speech, camera and permitted status access. Fake robot supplies simulated status/images and records requested speech without hardware.
- Action executor: typed, allowlisted commands, request IDs and completion/error feedback. Model text does not prove an action ran. No arbitrary robot RPC or generated code execution. Motion disabled initially.
- Session state: short-lived conversation context; explicit clearing; no automatic raw media or credential logging.

## Work possible without Pepper
1. Build the UI and application server with fake robot and fake provider.
2. Implement native audio playback and first-word acceptance checks.
3. Add provider interfaces, request deadlines/cancellation and accurate failure reporting.
4. Evaluate local model/runtime compatibility with the Mac's memory and CPU/GPU; obtain small test fixtures and benchmark locally when ready.
5. Test UI error cases and preserve a reproducible launch path outside Jupyter.
6. Maintain faculty progress, test results and decision records.

## Work requiring Pepper
1. Verify current head address and reconnect/status behavior.
2. Recheck speech and user-initiated head-camera capture through the new app.
3. Test Pepper microphones against the Mac using the same sentence, position and background noise; assess echo and first-word capture.
4. Evaluate touch-to-talk/tablet controls, if usable on the installed tablet environment.
5. Verify stop/cancel behavior and small explicit actions with an operator, only after motion authorization and validation are implemented.

## Model evaluation
Candidate local stack: whisper.cpp for speech-to-text, Qwen3-VL 4B via a local runtime for text/vision, and Pepper's existing speech output. This is a candidate, not a validated recommendation for this particular Mac. A smaller text-only model can be considered if vision causes excessive latency. No model downloads or additional installations have been started for this plan.

Compare the same tasks across local and Gemini baseline: full first-word capture, transcript completeness, response latency (median and tail, sample count included), task correctness, image identification, failures/timeouts, memory use, and offline operation. Use short contexts first. Locally hosted open weights avoid cloud busy responses but still require adequate hardware and can be slow or wrong.

Proposed conversation responsiveness target for discussion: most short responses begin within 3 seconds after the user finishes speaking. This is an acceptance target to test and revise, not a guarantee. On failure, provide a prompt spoken/local status and preserve stop/cancel responsiveness rather than silently waiting through long retry chains.

## Hardware placement
User-facing sensing and output should move toward Pepper where useful: cameras, microphones, speakers, touch and tablet. AI compute initially runs on the Mac or a university lab machine. A later dedicated nearby computer can remove the personal laptop from daily operation without forcing a modern model onto Pepper's older onboard hardware. Assess microphone/vision quality and network latency rather than maximizing onboard hardware usage as an end in itself.

## Pending product decision
Select one first demonstration: conversation with vision, visitor assistance, or spoken physical tasks. Conversation with vision is the provisional scope because its underlying hardware path has already been demonstrated. Confirm with the user/professors before expanding the demonstration scope.
