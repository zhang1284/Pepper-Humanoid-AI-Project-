# Pepper Research Limitations and Issues

Prepared for Michael Loiseau and the Pepper research team and faculty
November 5 2026 · Evidence through November 4 2026

Pepper has demonstrated useful speech, camera interpretation, tablet display, microphone capture, and supervised movement. The main unresolved barrier to the proposed Harper second floor guide is repeatable localization and reliable navigation to saved destinations. These results support continued development; they do not establish readiness for unattended public operation.

This report consolidates the project notes and operator observations. It distinguishes failures in the reference applications, issues in our prototypes, and capabilities that have not yet been tested. An observed failure is not treated as a permanent hardware limitation.

### Current position

| Area | Evidence and remaining limit |
| --- | --- |
| Localization | Fresh map localization succeeded; recovery from an offset failed in one controlled comparison. |
| Movement and cancellation | Short localized movement and cancellation during movement were observed in our app. |
| Saved destinations | Vendor app reached a saved point once. Our Home return trial logged an error and was later judged inconclusive. |
| Display | Native Android and embedded web panels worked. A Harper floor plan displayed in tablet Chrome. Legacy tablet API issues remain. |
| Audio and AI | Robot microphone speech was clear in one reviewed recording. Cloud requests had intermittent availability failures. |
| Complete application | Features remain split across research tools. The intended integrated RoboHearts replacement is not complete. |

### How to read the findings

Open means an observed issue has no verified resolution. Mitigated means a working alternative or prototype correction exists, with limits stated. Unverified means the required behavior has not been demonstrated. Inconclusive means the trial cannot support a clean pass or diagnosis. These labels are evidence descriptions, not statistical reliability ratings.

Sources are listed on page 7. No new hardware test was performed to prepare this report. Keys, passwords, participant recordings, and participant photographs are excluded.

## Localization reliability

### Repeated failures on an existing map

Open · November 4 · Sources R1 and R6. MapLocalize&Move repeatedly spun Pepper and ended with a red X and a retry prompt. Closing the charging flap did not resolve all failures. Our own Localization Lab later executed a scan on the recovered November 3 map and returned “Relocalization failed” at about 23.7 seconds, before its 25 second deadline.

The failure is therefore not limited to a confusing vendor screen. It also does not identify a defective sensor, an unusable robot, or an impossible Harper route. Map coverage, the active map, starting pose, environmental changes, and the underlying localization behavior were not independently isolated in every trial. The next useful evidence is repeated testing with one identified map and marked starting poses.

### A successful origin start does not establish recovery elsewhere

Open · November 4 · R1. With a freshly created map selected, one run localized at the original spot in 634 ms without visible base motion. After an approximately half meter shift, the next run turned through a full circle and failed at 23.7 seconds. A return to the original area later succeeded in about 8.2 seconds.

These are individual trials, not a success rate. They show that easy acquisition near the mapping origin cannot be generalized to arbitrary starting positions. Furniture movement was reported, but no experiment established it as the sole cause. No experiment established sound alone as a cause of localization failure.

### Stable reported pose and accurate physical pose are different

Unverified accuracy · November 4 · R1. A stationary 12 second observation produced reported positions within roughly 3 cm and headings within 1 degree. The operator confirmed no base turn in that trial. That supports short term consistency at that spot, not surveyed absolute accuracy.

During the later Home trial, displayed positions varied by several tenths of a meter. Physical stationarity throughout those samples was not established. It would be premature to call this proven localization drift. A controlled comparison against floor marks and measured displacement is still needed.

### The full Harper loop remains unqualified

Unverified · R1 and R8. The desk home position and triangular second floor route are deployment goals. We have not mapped and repeatedly navigated the whole loop, demonstrated recovery at each corner, or tested normal lobby traffic and operating lighting. Photos show glass, open areas, mats, furnishings, and strong accent lighting; they identify conditions to test, not proven causes of failure. Full floor mapping, route clearance, recovery behavior, and repeatable return to the desk remain acceptance work.

## Destinations and movement control

### Waypoint names can survive a change of map

Open in reference workflow and mitigated in prototype · November 4 · R1. After remapping a different room, the vendor app still showed test1 and test2 and navigated to places interpreted in the new map. Exact file reuse was not compared, so identical coordinate reuse remains an inference. Reusing a name does not verify that it still denotes the same physical destination.

Localization Lab 0.8 binds its saved Home coordinates to the SHA 256 fingerprint of the complete map file. A different file cannot use that record. This is implemented protection, not a completed end to end map migration test. It stores only one Home; saving again replaces it. It does not import the vendor test points.

### Our first Home return is not a verified navigation pass

Inconclusive · November 4 · R1 and R7. Home saved successfully and the forward action reported 0.30 m of map displacement. Go to Home then returned “GoTo failed to complete” before the observation timeout. The operator later reported a base turn toward them as Start One Scan was selected, disrupting the intended controlled setup.

The logged error is real, but that trial does not establish a repeatable Home return defect or its cause. A changed starting heading alone should not invalidate a correctly localized map pose. The coordinate convention was checked against the SDK documentation and matches its saved frame method. A clean save, forward move, and return remains pending.

### Background interaction can change the base direction

Partly mitigated · November 4 · R1 and R7. Pepper physically turned toward the operator, including an approximately 180 degree turn when approached from behind. A forward command then followed her current facing direction rather than the operator’s earlier intended direction. A background awareness response is plausible; its exact trigger was not isolated.

Version 0.7 added a hold on background base rotation during the test. The hold is acquired during scan preparation, leaving a window before acquisition. Clear acknowledgment that the hold is active and an explicit starting pose check are needed before another controlled Home trial. Scan controlled turning remains distinct from background turning.

### Successful commands are bounded evidence

Limited validation · R1. Earlier native moves of 10 cm and five feet were physically observed; the five foot run reported 1.522 m against a 1.524 m target. Our localized 30 cm test reported 24 cm, while the operator estimated roughly 30 cm using existing tape. Neither result is an independent precision measurement. One active move was cancelled promptly by operator observation; stopping time and distance were not measured. This is not emergency stop certification.

Version 0.8 deliberately caps Home return at 1 m, speed at 0.15 m/s, and uses straight line planning with a 120 second localized session. No obstacle course, crowded route, network loss during movement, or long duration navigation has passed. AI destination selection and spoken navigation are not integrated.

## Tablet and connectivity issues

### Legacy tablet requests did not produce the intended page

Open with a working alternative · November 2 to 3 · R1 to R4. Image and display requests sometimes produced revolving colored dots rather than the intended content. A tablet wake request did not clear them. Android Home navigation restored the home screen. The responsible foreground view and the underlying failure were not conclusively identified.

A later registry read did not contain ALTabletService even though the tablet notification said it was connected to Pepper via USB. A head connection or USB notification therefore does not establish that the legacy display service is available. Our native Android Activity and embedded web panel both rendered successfully, providing a separate working display route.

### A wrapper acknowledgment was not proof of display success

Confirmed integration issue · R1 and R4. Source inspection found that ipynao converted null or undefined method results to true. Some recorded true values were wrapper output rather than a raw success Boolean from Pepper. The replacement must preserve return types and distinguish request acknowledgment, action completion, and the physical result.

### One display update repeatedly stalled in installation

Open for that update path · November 3 · R1 and R3. Pepper Display Lab 0.1 installed and worked. Updating to 0.2 remained on Installing and produced Google Play Store stopped messages; a cache only recovery and retry did not resolve it. The old app remained launchable. This was an installation failure, not evidence of a slow APK download. Later Localization Lab updates installed, so APK updates are not universally broken. No crash trace established the cause.

### Native connection access differed from browser access

Mitigated with remaining lifecycle work · R2 and R3. Native tools initially returned No route to host while a browser notebook could connect. ChatGPT Local Network access was observed off; after the operator approved the permission change, native access improved and authenticated robot sessions later worked. This supports a permission related access issue, not a diagnosis of campus client isolation. Head, tablet, Mac, and local bridge addresses must be kept distinct. Reconnection and changing IPs still need product handling.

### Android focus and threading constrained our implementation

Mitigated in diagnostic · November 4 · R1 and R6. A direct Mac localization action was rejected with “Focus not owned.” An Android foreground Activity using the SDK focus lifecycle enabled the action. The first diagnostic crashed; a subsequent version exposed NetworkOnMainThreadException. Moving SDK operations off the UI thread enabled scanning. Focus loss requests cancellation; robust lifecycle recovery still needs testing.

### Device diagnostics and map selection remain awkward

Open workflow limits · R1 and R7. Android 6 and the installed software stack constrain compatibility. No authorized ADB device was available for crash logs. The file picker hid internal storage until Show SD card was selected. The app does not retain the selected map after reopening and defaults to the older bundled map. Temporary transfer servers also depend on the Mac being reachable and expire after 15 minutes. These are research workflows, not a finished installer or operator experience.

## Audio AI and notebook issues

### Mac recording clipped the opening words

Open in that capture path · November 2 · R1 and R5. All three local comparison recordings omitted opening words despite a ready cue. Browser native recording was reported much clearer than notebook style PCM and resampled PCM. One diagnostic showed 4.81 seconds wall time but 3.93 seconds captured PCM. That mismatch is evidence of an acquisition issue, not a complete diagnosis. Correct AI transcription cannot prove the source audio was intact.

Robot microphone capture provided a promising alternative: the operator heard clear speech, including the opening “one,” in the second local front microphone recording. This is one successful reviewed clip, not proof of reliable continuous conversation, echo handling, multiple speakers, or performance in a noisy lobby. The first capture had no spoken sentence and was transport evidence only.

### Available microphone services did not match simple examples

Mitigated experimentally · November 3 · R1 and R5. ALAudioRecorder was absent from the audited registry. ALAudioDevice subscription and a temporary receiver captured 16 kHz mono front microphone audio, with cleanup confirmed. Earlier receiver lookup attempts failed transiently; their exact cause was not proven. A reusable app must manage subscription ownership, readiness, interruption, reconnection, and cleanup without relying on a one time research script.

### Cloud model requests repeatedly failed or timed out

Open reliability issue · R1. Minimal requests to the models labeled gemini 3.7 flash and 3.8 flash in the experiments repeatedly returned 503 high demand messages or timed out. The model labeled 3.5 flash produced fast audible text, audio, and image answers but also encountered an intermittent 503. These are historical observations of those identifiers, not a claim about current model availability.

The key could access metadata; dashboard screenshots showed nonzero quota below displayed limits. AI Studio also produced a response. None of this guarantees API generation reliability or proves a billing problem. Generic local relay failures and automatic SDK retries initially obscured the upstream result. Better error extraction and bounded diagnostics improved visibility. Local or open weight models, provider fallback, and dependable offline behavior have not been benchmarked end to end.

### Notebook state and action wording were unreliable foundations

Partly mitigated · R1 and R9. Jupyter reported Saving failed and Permission denied despite apparent owner write bits and free disk space. A source backup existed, but recovery of all unsaved browser state was not established. We should preserve the professor notebook as a reference while keeping application state and reproducible builds in project files.

Earlier AI replies sometimes implied a movement had occurred when the interlock blocked it. Offline examples also showed that a keyword gate could accept movement wording inside negation; no resulting robot movement was demonstrated. The final controller needs validated commands and truthful requested, blocked, running, cancelled, and completed states independent of the model’s spoken answer.

## Remaining platform gaps

### The benchmark is not a completed parity assessment

Unverified · R1 and R10. The team reported that RoboHearts did not provide the needed document context, dependable floor plan display, image recognition, or AI directed movement beyond gestures. These are requirements and reported workflow limits; research has not established that every RoboHearts version universally lacks each capability. We have demonstrated some capabilities separately, but not complete parity in one replacement app.

### The components are not yet one deployed product

Open integration work · R7 and R9. Conversation Studio is a Mac hosted prototype with simulation and provider adapters. Localization Lab is a separate Android diagnostic. Display and microphone experiments use additional tools. A compiled APK, simulation pass, or capability registry entry does not establish an integrated conversation to navigation experience. Operation without a research Mac, startup after reboot, configuration, credentials, and recovery have not been accepted.

### Knowledge and memory remain limited

Unverified · R1 and R10. Separate turn recall of a favorite color demonstrated session context. Persistent memory, document ingestion with provenance, PDF question answering, controlled project instructions, and an editable building knowledge base are not complete. Reading a document must not authorize robot actions contained in it. Content freshness, room assignments, and destination aliases need team ownership.

### A displayed floor plan is not a navigation map

Partly demonstrated · R1 and R8. A real Harper PDF displayed successfully on the tablet, although the operator found the layout cramped. Touch routes were verified on a fictional floor plan; real building routes and live robot position were not. The first Harper PDF was dated May 2021; newer supplied floor plans were inspected separately. Room labels, navigation coordinates, map alignment, route connectivity, and no go areas still need validation. A PDF overlay alone does not give Pepper a trusted location.

### Vision and gestures need broader acceptance tests

Limited evidence · R1 and R4. One camera question correctly identified a held snack bag. That does not establish building landmark recognition, people tracking, visual localization, or camera and laser sensor fusion in our application. Animation inventory returned 267 entries, but inventory is not a custom gesture demonstration. Gesture selection, completion, interruption, and coordination with speech and navigation remain integration work.

### External voice and local inference are candidates

Unverified · R1. ElevenLabs was considered for speech output, with native Pepper speech as a fallback, but no end to end voice comparison or playback test was completed. External compute remains the initial implementation direction for heavy inference; performance on robot hardware was not measured. A no cost hosted service is not a reliability guarantee, and a local model still requires suitable compute and evaluation.

Unattended operation, approach and greeting behavior, crowds, repeated returns, accessibility, security of the final service, and sustained recovery from network or provider loss remain unqualified. No elevator, stairs, or multi floor escort capability has been demonstrated. The iJay store concept remains a future use case rather than a tested deployment.

## Priorities and evidence index

### Next acceptance gates

1. Establish a controlled start. Confirm the exact fresh map, the marked physical pose, and visible acquisition of the background rotation hold. Retain the old map and separate its destinations.

2. Validate Home return. Save Home while localized, complete one short move, and return under supervision. Record actual movement, completion or error, and position and heading against a physical reference. Repeat under defined conditions before treating it as reliable.

3. Qualify one lobby segment. Establish desk Home, map a short route to a nearby approved destination, and test acquisition, arrival, cancellation, and return before extending around the triangular loop.

4. Integrate the product. Join robot microphone input, bounded AI requests, truthful speech output, document context, tablet content, and approved destination selection. Keep navigation enforcement and recovery in the controller. Define repeatable acceptance criteria with the team before public deployment.

### Primary project evidence

Paths below are relative to the pepper conversation prototype project. Historical notes may retain older headings and pending statements; later dated corrections in the progress record take precedence.

R1  outputs/pepper-application/docs/RESEARCH-PROGRESS.md
Main chronological record, including operator corrections and November 4 trials.

R2  outputs/pepper-application/docs/LIVE-DISCOVERY-2026-11-02.md
R3  outputs/pepper-application/docs/LIVE-DISCOVERY-2026-11-03.md
Connection, registry, tablet, and permission investigation.

R4  outputs/pepper-application/docs/TABLET-AUDIO-CONTRACTS-2026-11-02.md
R5  outputs/pepper-application/docs/ROBOT-AUDIO-NEXT-2026-11-03.md
Interface evidence and microphone test criteria.

R6  outputs/pepper-application/research/room-map-import-2026-11-04/
Recovered map and point verification, map loading, and direct localization evidence.

R7  outputs/pepper-android-display/localization-lab/
HOME-TEST.md, BUILD-VERIFICATION.json, and LocalizationActivity.java source.

R8  outputs/pepper-android-display/harper-map/
R9  outputs/pepper-application/docs/IMPLEMENTATION-LOG.md
R10  outputs/pepper-application/docs/CAPABILITY-MATRIX.md
Map viewer evidence, prototype implementation, and capability investigation history.

### Maintaining this report

Update a finding when a controlled trial changes its status. Preserve failures and operator clarifications together. Record app version, map identity, starting conditions, expected and actual behavior, and cleanup. Keep physical observation separate from SDK acknowledgments. No faculty distribution has been performed; the team controls sharing.
