# Implementation log

## November 2, 2026 — standalone prototype 0.1
Created a local Python server and browser UI independent of Jupyter. Added native MediaRecorder capture/playback, a two-second startup warmup, microphone selection, typed chat, in-memory session context, provider selection, bounded requests and cancellation.

Providers: explicit simulation, Gemini generateContent text/audio, and local Ollama typed chat. No live provider calls made during implementation. No additional AI models installed or downloaded. Real Pepper integration is not yet implemented; robot mode is clearly simulated and motion has no execution endpoint.

Validation: four offline adapter tests and JavaScript syntax passed. Browser verification passed: typed “Hello Pepper” in the simulation provider, sent it through the app server, and observed the explicitly simulated response. No cloud service or robot was called. A CSS visibility issue found in the first browser inspection was corrected. Native recording and cloud/local inference need user/hardware evaluation; offline tests do not establish real-world reliability. Research progress and build-plan documents remain the record of previous notebook experiments.
