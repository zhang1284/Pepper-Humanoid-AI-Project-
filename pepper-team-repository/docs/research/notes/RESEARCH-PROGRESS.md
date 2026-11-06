# Pepper AI application: research and development record

Prepared for faculty review. Updated November 2, 2026.

## Objective
Build a standalone application that lets an AI converse through Pepper, use robot camera and sensor information, and eventually request a limited set of validated robot actions. The professor-supplied notebook is a working reference and experiment platform, rather than the final application.

## Evidence established in this project

| Capability | Evidence | Current limit |
| --- | --- | --- |
| Local computer-to-Pepper connection | The reviewed bridge connected to NAOqi and delivered speech commands. | Head IP can change; the UI's 127.0.0.1 address denotes the local bridge, not the robot head. Reconnection needs explicit health checks. |
| AI-generated speech | With gemini-3.5-flash, the user reported fast audible replies from Pepper. | User-observed speed; not a controlled latency benchmark. |
| Conversation context | Pepper recalled a previously stated color on a separate turn. | Session history only; not evidence of persistent or general long-term memory. |
| Camera and visual interpretation | Pepper's camera image was displayed; Gemini identified a held snack bag matching the image. | Single successful object example; not a general vision accuracy evaluation. |
| Motion interlock | The reviewed notebook/bridge blocked requested movement. | No physical movement has been validated by this review. Earlier model wording incorrectly implied execution; revised messages distinguish blocked actions. |
| Model-dependent availability | Minimal requests to 3.7/3.8 repeatedly produced 503 or timeout; 3.5 completed text, audio and photo questions. | Availability changes. We have not established a general service outage, a permanent free-tier restriction, or future reliability. |
| API/project access | Key-authenticated metadata requests succeeded; project screenshots showed nonzero quota and usage below displayed limits. | Model metadata access alone does not prove generation permission. No evidence that enabling billing is the required fix. |

## Audio finding: November 2
The user compared three clips captured in a local diagnostic:

- A: browser-native MediaRecorder output was reported as clearly better.
- B: PCM captured through the notebook-style ScriptProcessor at the original sample rate sounded worse.
- C: the same PCM converted to 16 kHz also sounded worse.
- All clips were reported to omit the opening words, even after the readiness cue.
- The screenshot showed 4.81 seconds wall time versus 3.93 seconds PCM captured. This is evidence of a duration mismatch, not a complete explanation of the missing opening words.

Interpretation: there is an audio acquisition/processing problem before AI transcription. Prefer native recording for the standalone application and test startup independently. Do not label it solved merely because AI inferred the right answer. A voice-processing toggle is available for a controlled follow-up comparison. No audio from this diagnostic was uploaded by the local test panel.

## What was learned from troubleshooting
The initial generic browser failure obscured the actual result. A loopback-only relay and official SDK test made failures observable. SDK retries prolonged diagnostic waits; diagnostic retries were disabled, and provider status/message extraction was corrected. Changes added complexity to the experiment platform, motivating a standalone app with explicit states, one configuration source, bounded requests, and modular providers.

The original notebook's recognition keyword gate is insufficient as an action-authorization mechanism: offline examples containing negation could pass its keyword test. No claim is made that these examples caused a robot movement. Future motion execution must be separately validated and independent of model wording.

## Open issues
1. Opening words missing in native audio; determine startup, device, or voice-processing contribution.
2. Notebook-style PCM capture quality/duration; replace in the app rather than continuing to patch it indefinitely.
3. Notebook save failure: Jupyter returns Permission denied despite owner write bits and free disk space. Source backup exists; unsaved browser state has not been fully recovered.
4. Intermittent cloud availability; evaluate local inference and provider fallback.
5. Pepper microphone streaming, tablet UI, robot reconnection, and physical actions remain unverified for the new application.

## Decisions and proposed direction
- Preserve the professor notebook and acknowledge its bridge/skills contribution.
- Build a new standalone local app, retaining only reviewed, useful bridge capabilities.
- Keep the AI provider replaceable; preserve Gemini 3.5 as a measured baseline, not a permanent dependency.
- Evaluate local speech recognition plus a small local vision-language model on available external compute.
- Prefer Pepper's microphones, cameras, speakers, touch sensors, and tablet for embodied interaction when their measured performance is adequate.
- Keep heavy AI inference off the robot initially. Revisit placement only after capability and performance measurements.
- Keep motion locked until speech, action validation, cancellation, and small controlled tests are ready.

## Data handling
Do not include API keys, passwords, participant recordings/photos, or full participant conversations in faculty documentation by default. This record uses capability summaries and synthetic test descriptions. No report has been sent to professors; the user controls sharing.

## Next faculty-review milestone
A standalone app demonstrates a complete recorded sentence, a timed AI response, Pepper speech, one user-initiated camera query, and graceful provider failure. Publish measured results, failures and remaining uncertainties alongside the demonstration. Hardware integration and motion are subsequent acceptance tests.

## Primary sources for candidate components (checked November 2, 2026)
- Google Gemini troubleshooting: https://aistudio.google.com/docs/troubleshooting
- Qwen3-VL-4B-Instruct model card (Apache 2.0): https://huggingface.co/Qwen/Qwen3-VL-4B-Instruct
- Ollama Qwen3-VL local model variants: https://ollama.com/library/qwen3-vl
- whisper.cpp local speech recognition and Apple Silicon support: https://github.com/ggml-org/whisper.cpp

## Tablet recovery and connector semantics — November 2
Operator confirmed normal Android home returned after touching the screen. The built-in image itself remains unverified. Inspecting the installed ipynao frontend revealed that its callService handler converts null/undefined results to true (`resolution ?? true`). The replacement application must preserve raw return types, distinguish command completion from physical display verification, and use completion/error signals where supported. See TABLET-AUDIO-CONTRACTS-2026-11-02.md.

### Tablet asset control failed
The existing notebook connection remains live. A preload-only request for the older vendor charger-help image returned false. This asset is not a known-good control for this robot; its failure must not be generalized to the complete tablet display feature. A separate event-listening socket failed before any subscription or operational call. No new visible display change was requested.

### Android components identified
Read-only tablet ping returned pong and confirmed LPT_200AR. Installed package inspection found 124 packages, including TabletBrowser 6.0.21, RobotService 2.3.2, WebView 95.0.4638.74, MapLocalizeAndMove 1.4.7 and Robohearts PepperChat 1.7.3. This makes the tablet browser and installed mapping application specific targets for further investigation. No apps were launched or modified; functional display and navigation remain unproven. Selected versions are retained in research/tablet-application-inventory-2026-11-02.json.

### Exact tablet recovery clarified
Tablet-only wake produced no visible change. The operator restores home by revealing Android navigation and pressing Home, not merely touching the dots. This supports investigating a foreground application/view; its identity remains unconfirmed. Android navigation responds normally.

### Session saved before class
Stopped live tablet tests at the operator’s request. No native dialog or blue web page was visually confirmed. The operator reported possible command-related flickering but was unsure. Timed cleanup requests completed; physical dismissal is unconfirmed. Next session should establish event delivery and foreground browser state rather than repeating asset guesses. Full details and recovery observations are in TABLET-AUDIO-CONTRACTS-2026-11-02.md.

## November 3 — fresh session identifies a missing display service
Operator reconfirmed head IP <local-network-IP> and Android home. A fresh notebook widget connected and ALSystem.systemVersion returned 2.9.5.172. ServiceDirectory.services returned 74 registrations, with ALTabletService absent. Therefore no new display test was attempted. This differs from yesterday's responsive tablet service; cause is not established. Asked operator to read the tablet's robot-service notification/status and tablet IP, without changing settings. See LIVE-DISCOVERY-2026-11-03.md and research/connection-service-check-2026-11-03.json.

Platform requirement: refresh the capability inventory after reconnecting and explicitly disable unavailable tools. Preserve null/void return values and correlate requests correctly; a connected head does not by itself establish tablet/display availability.

## November 3 milestone — native Android display physically confirmed
The team-built Pepper Display Lab 0.1 APK was downloaded through the temporary Mac transfer page, scanned by Play Protect (operator photo: “This app looks safe”), installed, and opened by the operator. A physical photo clearly shows the blue PEPPER DISPLAY TEST native panel, header and three buttons on Pepper's tablet.

This demonstrates our own Android Activity rendering on the actual hardware without ALTabletService. It establishes a display host for the larger platform; it does not explain the legacy dots/service issue or demonstrate robot/AI integration. Embedded WebView, return-to-native and exit/recovery are the remaining display acceptance checks. APK/build/signature evidence is in the sibling pepper-android-display/BUILD-VERIFICATION.json.

### Embedded web display confirmed — November 3
After the photographed blue native panel, the operator tapped Web page and confirmed the green embedded web panel was visible. Native Android and in-app WebView rendering are now physically demonstrated on Pepper using the standalone APK. This is an offline bundled-content demonstration, not yet a floor-plan asset, external web connectivity or robot-control integration test. Return-to-native and Close test recovery remain pending.

## Display acceptance passed — November 3, 2026
The operator confirmed the entire intended sequence worked: blue native panel, green embedded web panel, return to the native panel, and normal app closure. This establishes a physically tested standalone Android display host on the existing Pepper tablet. No robot motion or model connection was part of this test.

Next milestone: a bundled, touch-interactive floor-plan demonstration using this app, with clearly marked sample content until a real building plan is supplied. Displaying directions and navigating the physical robot remain separate capabilities. The older ALTabletService failure remains unexplained; the new route works without it.

## Implementation increment: floor-plan demo 0.2 prepared
Built version 0.2 of the native tablet app with a bundled fictional layout, four destinations, touch/button selection, route highlighting and sample written directions. Existing native/web diagnostics remain. No Android permissions or robot controls were added. Build/lint passed (0 errors, 2 non-blocking warnings), APK v1/v2 verified and signing certificate matches installed 0.1. A temporary transfer page was locally checked against the APK hash. Installation and physical touch/route acceptance of 0.2 remain pending.

This moves the display workstream from interface discovery into feature implementation. Discovery continues independently for microphones, robot SDK integration, gesture execution and navigation. See DISCOVERY-TO-IMPLEMENTATION-2026-11-03.md for the evidence/next-deliverable matrix. The overall Robohearts replacement goal remains unchanged.

### Version 0.2 installer delay
Operator reported a Play Store stopped-responding message followed by about 3–4 minutes on the installer. The supplied photo explicitly shows Installing, not a browser download progress screen. The 28,606-byte app has reached the installer; update completion is unconfirmed. Cause is unknown; the Play Store report is temporal evidence, not a proven diagnosis. No uninstall, reboot, app-data clearing, force-stop or security changes requested. Next check is Home → Pepper Display Lab and visible version 0.1 / 0.2, or whether launch is unavailable. Version 0.1's prior display acceptance remains valid historical evidence.

### Installed version established
The operator opened the actual installed app icon, not an APK file in Downloads. Photo clearly shows Pepper Display Lab 0.1 and its original blue panel. Version 0.2 installation has not completed. The working 0.1 installation remains launchable; floor-plan runtime testing has not begun. Download entries for both APKs are present; opening those entries invokes installation rather than launching the installed Activity.

### Cache-only recovery failed; separate browser preview prepared
Operator reported completing the recommended cache-only Play Store step and one update retry; Play Store still crashed after a longer wait. Stopped repeated installation attempts. No request to clear Play Store data, disable Play Protect, uninstall the working app, reboot Pepper or change debugging/tethering was made.

Prepared a separate self-contained fictional floor-plan browser preview. Local browser checks confirmed the Lab destination button and Classroom room tap update selection/directions; a screenshot confirms the Classroom route visually. Started the existing restricted temporary server in preview-only mode, with no APK downloads exposed. Physical tablet Chrome interaction is pending. This is a UI validation fallback, not proof of a native app update or robot navigation. Native installer diagnostics remain unresolved.

### Floor-plan browser display confirmed
Operator reports the fictional floor-plan preview looks good on Pepper. Physical Chrome rendering is confirmed; touch-triggered route/direction changes have not yet been explicitly confirmed. This adds a working floor-plan display demonstration on the physical tablet, while the native 0.2 installation remains unresolved.

### Floor-plan browser acceptance passed — November 3
Operator explicitly confirmed tapping Lab and Classroom changes both the blue route and written directions on Pepper. Fictional floor-plan rendering and touch interaction are now physically verified in tablet Chrome. This is separate from the native 0.2 update, which remains uninstalled/unverified after Play Store crashes. No real building directions, localization, shortest-path navigation or robot movement were demonstrated.

Next implementation step: replace the fictional layout with a supplied real floor plan and verified destination/route data. In parallel, diagnose the Android update path using installation logs rather than repeated blind retries. Preserve the working native 0.1 app and browser preview as separate verified evidence.

### Real Harper map viewer prepared and locally verified — November 3
The operator supplied Creighton’s four-page Harper Building Map PDF. Preserved the original, inspected every page, rendered faithful vector floor drawings and indexed 92 printed room/place labels with source-page/text/bounding-box provenance. Implemented four-floor selection, search across floors, zoom/fit and selected-label markers. Source metadata dates the PDF to May 2021; current room assignments remain unverified. No walking routes or live robot position are asserted.

Local browser checks visually confirmed all four floor drawings, iJay selection, room 3018 search switching to floor three, Analytics Lab search/marker, and floor-reset behavior. See sibling pepper-android-display/harper-map/README.md, VERIFICATION.json and viewer-preview.jpg. Physical Pepper acceptance of the real-map viewer is pending. The temporary restricted server serves /harper without another APK installation; Android 0.2 update failures remain a separate unresolved issue.

### Real Harper map physically displayed — November 3
Operator confirms the real Harper viewer displays nicely on Pepper, with a cramped layout acceptable for this test. Physical display is verified; real-map search/touch acceptance was not separately reported. Prior fictional-map touch acceptance remains separate evidence. Next priority is robot microphone capture, then bounded movement; tablet installation failure and display layout improvements remain open workstreams.

### Fresh robot-audio interface audit — November 3
Direct native qi session succeeded. ALAudioDevice, ALSpeechRecognition and RoboHeartsSpeechReco are registered; ALAudioRecorder is absent. Saved current schemas and closed the session. No recording or preference changes occurred. Next experiment and acceptance criteria are documented in ROBOT-AUDIO-NEXT-2026-11-03.md. Microphone quality remains unverified.

### First robot microphone stream captured — November 3
After explicit operator readiness, created a unique temporary qi audio receiver and captured 16 kHz mono front-microphone audio locally. First callbacks arrived after 0.136 seconds in the successful attempt. Saved 135,135 samples (8.446 seconds, including a half-second lead-in), 99 callbacks, maximum observed callback gap 0.127 seconds. Unsubscribe and receiver unregistration completed; the session closed. No cloud upload or robot-side recording file. Playback intelligibility and full-sentence onset remain pending operator review. Earlier attempts failed before subscription with receiver lookup errors; the successful attempt additionally confirmed registration in the session service registry. The precise cause of the transient lookup failures is unproven. Evidence: research/robot-audio-capture-2026-11-03.json and local WAV.

### Pepper front microphone playback passed — November 3
The first clip established audio transport. The operator did not speak during that capture. In take two, the operator reported clear speech and explicitly heard the opening word “one,” which had been missed in earlier Mac microphone testing. Take two captured 8.531 seconds of 16 kHz mono audio locally. The receiver unsubscribed, unregistered, and closed the session. Evidence: research/robot-audio-capture-2026-11-03-take2.json.

### First controlled base movement — November 3
With the operator present and confirming clear floor, sent one 10 cm forward move with base collision protection enabled. The command returned success and odometry reported 9.88 cm displacement. The immediate movement flag remained active after a stop request. A fresh follow-up showed the base idle with the same reported XY position. Physical observation is pending. This test did not isolate a mid-motion stop. Evidence: research/base-movement-2026-11-03.json.

The operator visually confirmed the short move and full stop. At the operator's request, sent a single five-foot forward command on the reported clear floor with collision protection enabled. Pepper returned success and reported 1.522 m displacement against a 1.524 m target. The stop request completed and the base reported idle afterward. Evidence: research/base-movement-five-feet-2026-11-03.json.

The operator visually confirmed the five-foot move worked as expected. Next navigation milestone: use the installed MapLocalizeAndMove app to inspect the mapping workflow, then verify that Pepper can relocalize and reach one saved waypoint in a supervised room. The documented QiSDK mapping action starts with a rotating base and head scan, so inspect the app before starting any action.

### Saved waypoint navigation — November 3
The operator mapped the room in MapLocalize&Move, saved test1 and test2, and reached the Production Mode destination list after localization. On one supervised GoTo, the operator reported that Pepper reached the selected spot and matched the saved position. The operator described the movement as fast. The specific waypoint, measured speed, and exact app completion status were not recorded. This establishes one physically observed navigation run through the vendor app. Repeatability, stop behavior with obstacles, and integration with our application remain open.

### Reusing the waypoint test map
SoftBank's MapLocalize&Move reference implementation documents saving the serialized QiSDK ExplorationMap and the named waypoint poses under tablet shared storage at sdcard/Maps. The two files need to stay paired because waypoint coordinates are relative to the saved map origin. Next check: inspect that folder on the installed tablet build and identify the map and waypoint files, then test loading the pair and resolving test1/test2 in our app before enabling GoTo. The files and compatibility have not been verified on this tablet.
Source: https://github.com/softbankrobotics-labs/maplocalizeandmove

### November 4 — iJay pilot and voice output
An authenticated read confirmed the same head endpoint is reachable and exposes ALAudioPlayer, ALAudioDevice, ALTextToSpeech, and ALNavigation. No playback or movement was requested. The saved map and waypoint files still need to be located in tablet internal storage under Maps.

ElevenLabs is a candidate speech-output provider. The app can send generated reply text for synthesis and play the returned audio on Pepper. Verify the speaker playback path with a short sample, then compare end-to-end latency and intelligibility against native speech. Keep native speech available for fallback. Provider reference: https://elevenlabs.io/docs/api-reference/text-to-speech/stream

Proposed iJay pilot: greet from a fixed welcome point, answer questions from a staff-reviewed product catalog and FAQ, and map product categories to a small set of named display waypoints. Maintain current prices and stock separately from stable specs and only answer availability from an authoritative source. Start with one supervised route outside peak traffic. Verify repeated arrivals, stop/cancel behavior, and a verbal-directions fallback when a route is blocked before expanding the route set.

### Saved navigation files recovered — November 4
The tablet Maps folder showed mapData.txt and points.json dated November 3. The operator copied both to the Mac through a temporary local page. The map is 17,148,988 bytes. The waypoint file is 176 bytes and holds a JSON string wrapped in Java serialization. Decoded it without deserializing Java objects and verified that test1 and test2 have finite x, y, and heading values. The originals, checksums, and parsed waypoint data are in research/room-map-import-2026-11-04. The map has yet to be loaded by our app, and no new localization or movement was run.

### Imported waypoint preview in our app — November 4
Added Check imported map to Conversation Studio. The loopback app verifies the original map and waypoint file hashes and reads test1 and test2 directly from the saved waypoint file. Verified the page and preview endpoint on an isolated local server, and the existing four app tests passed. This proves file pairing and waypoint parsing in our app. The map itself still needs to be opened by QiSDK on Pepper and used for localization before a GoTo action can be tested.

### Recovered room map opened by Pepper — November 4
The Mac research client verified the map checksum and passed the recovered archive to the live Mapping.makeMap service. Pepper returned an ExplorationMap and its top-view geometry at 0.05 m per pixel. The saved test1 and test2 poses projected to approximately (84.47, 63.50) and (92.23, 20.35) in map image coordinates. The Python binding exposes the geometry as a named property rather than the Java getter. Evidence: research/room-map-import-2026-11-04/ROBOT-MAP-VERIFICATION.json. This check instantiated the map only. Robot localization and GoTo remain to be tested through our own integration.

### Relocalization from an offset starting pose — November 4
In the previously mapped room, Pepper started near but not at the original mapping pose. The operator reported repeated Production Mode localization scans ending in a red X and a retry prompt. The saved map had already opened through the separate Mac research client. Cause remains unknown. Next control: repeat localization once at the original MapFrame position and heading with the charging flap closed and the area clear. Preserve the recovered map and waypoint files.

### Old waypoint names persisted after remapping — November 4
After creating a fresh map in another room, the operator reported that MapLocalize&Move still listed test1 and test2 and drove to both in the new environment. The operator observed that the destinations appeared relative to the new map. This is consistent with the reference app reloading named poses and attaching them to the active map frame. The new map and waypoint files were not copied or compared, so exact coordinate reuse remains an inference. The run shows that navigation worked on the fresh map, but the inherited names were not verified destinations there. Our app should bind waypoints to a specific map version and require re-teaching or re-verification after remapping.

### Decision after repeated localization failures — November 4
The operator reported further repeated localization failures in MapLocalize&Move after an earlier successful two-target run on a fresh map. Do not infer that the Harper second-floor loop is feasible or impossible from these room trials. Replace the vendor test UI with an instrumented QiSDK localization diagnostic before committing to a new localization engine. Load a verified map, record the scan status and action result, log the map-relative pose if available, and cancel at a fixed deadline. Compare repeat runs from a marked starting pose and a small offset. Distinguish a failed scan, a rejected action, a timeout, and successful localization. Keep GoTo separate and block dispatch if localization is unavailable or a waypoint belongs to a different map.

Harper pilot: qualify a short segment with stable landmarks, then test adjacent segments and recovery at turns before attempting the loop. If native localization remains unreliable at a specific spot, evaluate fixed visual markers as additional position references. Marker integration and full-loop navigation are untested.

### Custom localization preflight passed — November 4
The Mac client opened the verified November 3 map, created a ContextFactory context and constructed the Mapping.makeLocalize action. The live action exposes run, runWithLocalizationHint, and a status property whose initial value was 0. Evidence: research/room-map-import-2026-11-04/LOCALIZATION-PREFLIGHT.json. Added an explicit supervised scan mode to research/probe_localization.py with status timing, action errors, a cancellation deadline, and a base-idle check. The scan path is not hardware-verified yet. Confirm the robot is in the original mapped room and has clearance to turn before running it.

### Direct localization request rejected for focus — November 4
With the operator ready in the original room, the Mac client loaded the recovered map and requested one localization action. The action returned “Focus not owned” before a scan status transition. The base reported idle afterward. This is an integration permission result, distinct from the vendor app's red-X scan failures. Use the documented Android QiSDK activity lifecycle and its focus-granted context for the next custom scan test. Evidence: research/room-map-import-2026-11-04/LOCALIZATION-SCAN.json.

### Tablet localization diagnostic built — November 4
The operator closed the charging flap and reported that MapLocalize&Move still showed its red-X retry screen. The identity of the tablet app's active map is unverified after the newer room was mapped. Built Pepper Localization Lab as a separate Android app using QiSDK 1.7.5. It waits for robot focus, bundles the checksum-verified original room map, and offers one operator-started scan with timestamped status, errors, cancellation, and a 25-second deadline. Gradle build and APK signature verification passed. The bundled map hash matches the original recovered map. Tablet installation and live scan behavior remain unverified.

Localization Lab 0.1 installed and opened on Pepper. After the operator pressed Start one scan, Android showed “Unfortunately, Pepper Localization Lab has stopped.” No ADB device was available to retrieve the crash trace. Version 0.2 streams the bundled map from a cache file via the vendor's StreamableBuffer interface, guards scan setup errors, and persists the last stage before SDK calls for inspection after a crash. Build and APK signature verified. Root cause and tablet behavior of the update are pending.

Localization Lab 0.2 installed, received robot focus, and reported android.os.NetworkOnMainThreadException when starting the scan after map preparation. Built and signature-verified 0.3, which moves QiSDK listener registration, scan dispatch, and cancellation off the Android UI thread. The new scan is pending a tablet retest.

### First live localization scan in our tablet app — November 4
Localization Lab 0.3 obtained robot focus, opened the checksum-verified November 3 room map, and reported SCANNING at 123 ms. The operator observed physical scan movement. At 23,694 ms the status returned to NOT_STARTED and QiSDK reported “Relocalization failed.” This occurred before the app's 25-second cancellation deadline. No LOCALIZED status was observed. The threading fix enabled scan execution, but the cause of failed position matching remains unknown. Next controlled test: use a freshly saved map and immediately localize from its marked origin and heading, keeping the same verified map for both steps.

Built and signature-verified Localization Lab 0.4 with an Android document picker for a fresh mapData.txt. The chosen file is streamed into QiSDK for a localization-only test. The bundled November 3 map remains the default. The fresh-map picker and scan have yet to be verified on Pepper.

### Fresh-map localization passed in our tablet app — November 4
After creating and saving a fresh room map in MapLocalize&Move, the operator selected it in Localization Lab 0.4 and ran a scan. The tablet photo shows robot focus granted, selected map opened, SCANNING, LOCALIZED, and “Localized successfully.” The app then cancelled its diagnostic action by design. This verifies fresh-map import and localization on physical Pepper. Repeatability from an offset and waypoint navigation from our app remain unverified.

### Fresh-map offset test — November 4
Localization Lab 0.4 showed SCANNING at 125 ms and LOCALIZED at 634 ms on the first fresh-map run. After the operator reported moving Pepper approximately half a meter to the right in the room photo, a second run using the same selected map showed SCANNING at 116 ms, then NOT_STARTED at 23,695 ms with “Relocalization failed.” This is one successful origin start and one failed offset start. Heading and exact displacement were not independently measured. Next control: return to the original mark and heading, then repeat with the same map to check whether success at the origin is reproducible. Continuous localization after a successful start remains untested in our app.

The operator clarified that the fresh-map success at the origin had no visible robot movement. During the failed offset run, Pepper visibly turned through a full circle. The status event alone therefore does not establish physical scan motion, and the fast origin success does not establish recovery from an arbitrary pose. The return-to-origin trial was pending when this observation was reported.

### Return-to-origin localization — November 4
On the third scan with the same selected fresh map, Localization Lab 0.4 reported SCANNING at 124 ms, LOCALIZED at 8,157 ms, and NOT_STARTED at 8,206 ms after automatic cancellation. The operator observed Pepper turn and finish facing roughly 180 degrees from her starting heading. The app does not record the map-relative pose, so the reported heading estimate cannot be checked against the physical observation. Fresh-map trials now show two reported localization successes at the original spot and one failure after an approximately half-meter offset. Next step: keep localization active after acquisition and report map-relative position and heading before any GoTo test.

Built and signature-verified Localization Lab 0.5. Once localized it keeps the action active for a 12-second stationary observation and reads the robot frame relative to the map frame on a background thread. It reports x/y in metres and heading in degrees. Pose reading, stability, and terminal behavior still need a tablet test. Reference: https://android.aldebaran.com/sdk/doc/pepper-sdk/ch4_api/movement/reference/localize.html

### Stationary map pose verified on Pepper — November 4
Localization Lab 0.5, using the selected fresh map, reported SCANNING at 143 ms and LOCALIZED at 3,181 ms. Four displayed map poses (x m, y m, heading degrees) were (-0.05, -0.10, -6), (-0.03, -0.12, -7), (-0.03, -0.12, -7), and (-0.04, -0.12, -7). The observation window completed and the app cancelled the action as designed. The displayed position varied by less than 3 cm between samples and heading by 1 degree. These are repeatability readings while stationary, not independent absolute pose measurements. Operator identified the map's original facing direction as away from the door. Final physical heading was not yet explicitly confirmed. Tracking during motion remains the next test.

The operator confirmed that in the 0.5 stationary trial Pepper looked left and right, then raised her head. Her body did not visibly turn from its original orientation facing away from the door. This is qualitatively consistent with the reported heading near the map's original direction. The physical heading was not measured in degrees.

Built and signature-verified Localization Lab 0.6 for the first movement-under-localization trial. It offers one operator-triggered 0.30 m straight GoTo at a 0.15 m/s speed cap after a successful map pose read. It logs pre/post map displacement and requests cancellation on localization loss, focus loss, operator cancel, or observation deadline. Physical movement and cancellation behavior are pending tablet verification.

### Localized movement window expired before command — November 4
Localization Lab 0.6 reported LOCALIZED at 5,641 ms. The operator observed the forward button become available and later disappear as Pepper looked toward them. The photo ends with “Pose observation complete” and “Scan cancelled,” consistent with the programmed 30-second observation expiry. No movement-command log is visible. Several poses were near x -0.03 to -0.06 m, y -0.11 m, heading -7 to -8 degrees, followed by reported headings +90 then +55 to +58 degrees while x/y remained nearby. Whether the base physically turned is unconfirmed. Clarify head-only versus base motion before authorizing the forward test, since a large erroneous heading change would affect the target direction estimate.

### Physical base turn explains heading change; first 30 cm button trial — November 4
The operator confirmed that the earlier heading change accompanied a real base turn. This supersedes the initial uncertainty about a possible map-heading jump. In the next trial Pepper reportedly rotated roughly 180 degrees toward the operator approaching from behind, then completed a forward movement after the 30 cm button was pressed. Exact travel and map displacement were not recorded. The button's target was relative to Pepper's current facing direction, so the test did not preserve the operator's intended original direction. A background-awareness response is plausible but not proven from the observation.

Built and signature-verified 0.7 using the documented HolderBuilder constraint on ROBOT_FRAME_ROTATION during the scan/observation action. It releases the hold on action termination and failed setup. Added a visible 60-second countdown and an explicit current-facing-direction movement label. Runtime hold behavior, cancellation, and the repeated forward test remain pending. Reference: https://android.aldebaran.com/sdk/doc/pepper-sdk/ch4_api/abilities/reference/autonomous_abilities.html

### First localized GoTo completed in our tablet app — November 4
The operator confirmed that Localization Lab 0.7 worked. The photo shows “Move finished. Map displacement: 0.24 m.” after the one 0.30 m forward target. Localization remained active with 24 seconds left in the observation window. Before movement the displayed pose was about x -0.03 m, y -0.11 m, heading -9 degrees. Subsequent samples were near x 0.15 to 0.20 m, y -0.11 to -0.22 m, heading -8 to -10 degrees. No large heading turn is shown during this trial. The 0.24 m reported displacement is 0.06 m short of the requested offset; physical distance was not independently measured and arrival tolerance is not yet characterized. This verifies one small SDK GoTo while localization remained active in our own tablet app. Repeated target arrival, measured accuracy, and stop/cancel under motion remain open checks.

### Cancellation code review — November 4
The operator reported an existing tape reference and estimated the completed physical travel at roughly 30 cm, possibly slightly less; no ruler measurement was available. Another distance-only repetition was deferred. Reviewed Localization Lab 0.7: the Cancel scan or move button requests cancellation of both active GoTo and Localize futures, and move preparation checks the cancellation flag before starting. This is source inspection, not verification of physical stopping distance or latency. The scan completion message can appear before the movement future finishes cancelling, so a screen message alone must not count as proof that the base has stopped. A supervised short cancellation trial remains pending.

### Operator cancellation during active movement confirmed — November 4
Localization Lab 0.7, with the fresh map selected, reported LOCALIZED at 3,155 ms. The supplied tablet photo shows “Preparing one 30 cm forward move,” followed by “Operator cancelled,” “Scan cancelled,” and “Move cancelled.” The operator explicitly confirmed pressing Cancel immediately after the base started moving and observing the movement stop as intended. This provides one supervised physical observation of cancellation during active GoTo, together with SDK cancellation acknowledgments. Stopping time and distance were not measured; this does not establish emergency-stop performance or repeated reliability. No further motion was requested. The next development step is a saved destination bound to the same fresh map, with localization and cancellation retained.

### Map-bound Home destination prepared — November 4
Built Localization Lab 0.8 and passed Android lint and v1/v2 APK signature verification. Added Save current place as Home and an operator-triggered Go to Home, using freshly captured map x/y/yaw. The private saved record includes SHA-256 of the exact map archive; a different archive cannot use it. One Home record is stored, and saving again replaces it. Return is limited to 1 m from current localized position, straight-line-only planning, and a 0.15 m/s speed cap. Existing cancellation, localization-loss and focus-loss handling remain. Session observation is now 120 seconds to allow saving and two short commands. Physical Home save/return and persistence across relaunch remain pending; build and lint do not verify navigation accuracy. Installation alone triggers no scan or movement.

### First Home trial: save and forward pass; return fails — November 4
Four operator photos show Localization Lab 0.8 using fresh map fingerprint prefix f66df19765b2. Initial localization completed at 5,633 ms. Home saved at x 0.33 m, y -0.18 m, heading +82 degrees. Forward action reported completion and map displacement 0.30 m. Return then reported com.aldebaran.qi.QiException: GoTo failed to complete, before the observation window expired. The later normal observation expiry is separate from the return failure. Position samples vary by several tenths of a meter (including x 0.04 to 0.44 m in the final photo), with headings approximately +80 to +84 degrees. Physical motion/stationarity during those samples and whether the return moved the base are not yet confirmed; the images alone do not establish localization drift or an obstruction. Checked the official Localize and FreeFrame persistence documentation: robotFrame.computeTransform(mapFrame) and recreating the target with freeFrame.update(mapFrame, transform, 0L) match the documented coordinate convention. No coordinate reversal or automatic retry was applied.

### Operator clarification and session stopping point — November 4
The operator reported that Pepper turned her base toward them just as Start One Scan was selected in the 0.8 Home trial, before the intended controlled starting conditions were established. Mark this trial inconclusive for controlled Home-return performance. The SDK failure and saved log remain observations; the base turn is not proven to be the cause of GoTo failure, and a changed initial heading alone does not invalidate a correctly localized map pose. The rotation hold is acquired during scan preparation, so it does not prevent a turn before acquisition. A future revision should visibly acknowledge acquisition before the operator begins the controlled trial. Confirmed earlier results remain: localized short movement in 0.7 and operator cancellation during active movement. The operator is ending the session and intends to leave a floor arrow as a reference. Next session: verify the same fresh map, establish the marked starting pose, confirm the background-turning hold is active, then perform one controlled Home save/forward/return trial. No further physical actions requested this session.

### Intended deployment: Harper second-floor desk and triangular loop — November 4
The operator supplied two lobby photos and clarified that the team intends Pepper to be stationed beside the reception/welcome desk, with eventual navigation around the triangular second-floor circulation route and back. The photos show open tiled lobby space, desk/partition, columns, glass storefronts and entrance glazing, chairs, floor mats, and strong blue accent lighting. They do not show the complete loop, dimensions, all hazards, or daytime traffic. Treat the triangular route as a user-defined deployment requirement, not a mapped or validated robot route. Proposed progression: establish a repeatable desk Home/localization area; validate one short desk-to-destination return trip; extend through measured/verified intermediate waypoints around the loop. Test operating lighting and normal pedestrian conditions before claiming deployment readiness. The AI should resolve requests to approved destinations; the navigation controller must execute and monitor the route, stop on uncertainty, and retain operator cancellation. Room-level movement and cancellation success do not yet establish lobby localization or full-loop feasibility. Photo references: B73C849C-5974-4B11-A03E-0B1CC86B59ED/1-Photo-1.jpg and 2-Photo-2.jpg.

### Limitations report and documentation entry point — November 5
Created a seven page faculty and team limitations report from existing evidence through November 4, with findings separated into open, mitigated, unverified, and inconclusive states. No new hardware test was performed. Word and Markdown versions and START HERE.txt are in the project root Pepper Research Documentation folder. Original Research Notes links to the existing docs directory. A same named shortcut in Documents/Codex provides a visible Finder entry point.
