# Robohearts replacement: capability investigation

Updated November 2, 2026. This matrix distinguishes evidence from planned validation. No product-wide Robohearts parity claim is made.

| Area | Evidence already available | Next hardware investigation | Acceptance demonstration |
|---|---|---|---|
| Speech output | Plain speech from the reviewed bridge, audible AI replies reported by user | Service methods and ongoing status; interruption contract | Speak a supplied sentence, report completion, cancel cleanly |
| Robot microphones | ALAudioDevice and ASR-related services registered in earlier inventory; raw audio unverified | Metadata, streaming/subscriber interface, installed ASR contract | Short explicitly started robot-microphone capture with local playback and cleanup |
| Vision | Head-camera capture and one successful image question | Camera options, stream ownership and cleanup | Capture only on request, display exact image, answer an image question |
| Tablet/maps | Android 6.0 identified; tablet service exists; floor-plan display reported broken | Full tablet method signatures, connectivity and supported URL/image/display paths | Show a supplied floor plan on the physical tablet; then clear/restore appropriately |
| Gestures | Services/installed behavior packages present; no review-mode physical gesture verified | Available behavior and animation schemas/lists, arm availability | Execute a selected explicit gesture with truthful completion state and stop |
| Base movement | ALMotion and ALNavigation registered; reviewed notebook blocks actuation | Method schemas, coordinate frames, localization/map/status interfaces and obstacles | Separately authorized small explicit movement with measured result and stop |
| Navigation | Not demonstrated | Establish whether mapping/localization is available and usable on this installation | Navigate a constrained approved route only after localization/obstacle/stop tests |
| Knowledge/context | New requirement; no document ingestion implemented | Does not require robot for initial work | Ingest supplied PDF/project instructions with provenance, answer building questions with citations |
| Team extensibility | New platform plan and initial conversation harness | Define typed tool/plugin contracts and robot capability manifest | Add a documented knowledge source or allowed tool without editing core UI |
| Reliability | Gemini 3.5 successful but intermittent 503s; native audio clearer than PCM, beginning still missing | Reconnection, provider failure/cancellation, native capture validation | Repeated complete-turn tests with measured latency/failures and no false actuation claims |

## Audit sequence
1. Read service registry and selected service schemas. Only systemVersion and speech-language getters are included in the first automated audit.
2. Inspect exact schemas and public vendor documentation before choosing additional operational methods.
3. Propose one test for tablet, robot audio, gesture or base motion. Distinguish metadata from real functional evidence.
4. Obtain action-specific authorization when the test records people, changes tablet output, or actuates the robot. Do not enable all robot RPC to facilitate discovery.
5. Record trigger, preconditions, expected/actual result, side effects and cleanup for each test.

## Connection check — November 2
User reconfirmed head IP <local-network-IP> and reported CUWireless. Local bridge reports disconnected. Both 9503 and 9559 returned OS error 65 (No route to host). The Mac route uses Wi-Fi en0. This does not prove the IP is wrong, that CUWireless isolates devices, or that Pepper services are stopped. No network settings, robot configuration, motion or recordings were changed during this check. VPN state has not been established.

Next connection check: use the existing notebook connection cell once to distinguish the notebook process's access from the shell check. If both fail, compare the actual network on Pepper's tablet with the Mac and involve the campus network administrator if client isolation is confirmed. Do not bypass security controls or change robot network configuration speculatively.

## Later November 2 update: live browser route verified
Fresh Robohearts widget requests succeeded at the same IP despite the native-port failures. See LIVE-DISCOVERY-2026-11-02.md. The read-only motion status/protection getters and moveTo documentation succeeded; tablet legacy introspection failed, behavior manager was unavailable, and the navigation list does not match older public navigation examples. The investigation is no longer wholly blocked by connectivity, but native standalone connectivity remains unresolved.

## Tablet functional test update
Built-in charger-help image request and hideImage both returned true, but the operator saw revolving colorful dots and confirmed they remained after cleanup. Physical image display and restoration are unverified. Wi-Fi getter still reports CONNECTED; no descriptive error or current-activity information was obtained. See TABLET-AUDIO-CONTRACTS-2026-11-02.md for exact requests and limitations.

### Operator recovery correction
Touching the tablet restored normal Android home. Recovery is physically confirmed after touch, not attributed to hideImage alone. Source inspection also found ipynao replaces null/undefined method results with true; recorded Python true results are wrapper observations rather than necessarily raw robot booleans.

## Fresh tablet/animation schema and inventory
Raw gateway metadata retrieval now works independently of the notebook method-call wrapper. Tablet completion/error signal signatures are confirmed, though delivery and actual display remain untested. Animation inventory returned 267 entries, including 246 standard Stand paths across eight categories and 23 tags. No animation executed. See TABLET-AUDIO-CONTRACTS-2026-11-02.md and research/tablet-animation-interface-2026-11-02.json.

## Tablet application inventory follow-up
Tablet direct ping returned pong; model LPT_200AR; 124 installed packages. TabletBrowser 6.0.21, RobotService 2.3.2, Android WebView 95.0.4638.74, MapLocalizeAndMove 1.4.7 and Robohearts PepperChat 1.7.3 were confirmed installed. No app launched or changed. Mapping/localization is a concrete investigation lead, not a demonstrated navigation capability. Embedded PNG preload also returned false; data URI support remains unproven.

## November 3 — native display route confirmed
Operator installed/opened Pepper Display Lab and supplied a photo showing the intended blue native Android panel. Our own Activity can render on the tablet independently of ALTabletService. WebView, sample floor-plan content, switching and exit tests remain pending. This is not yet robot/AI integration.

### Embedded web display confirmed — November 3
After the photographed blue native panel, the operator tapped Web page and confirmed the green embedded web panel was visible. Native Android and in-app WebView rendering are now physically demonstrated on Pepper using the standalone APK. This is an offline bundled-content demonstration, not yet a floor-plan asset, external web connectivity or robot-control integration test. Return-to-native and Close test recovery remain pending.

## Display acceptance passed — November 3, 2026
The operator confirmed the entire intended sequence worked: blue native panel, green embedded web panel, return to the native panel, and normal app closure. This establishes a physically tested standalone Android display host on the existing Pepper tablet. No robot motion or model connection was part of this test.

Next milestone: a bundled, touch-interactive floor-plan demonstration using this app, with clearly marked sample content until a real building plan is supplied. Displaying directions and navigating the physical robot remain separate capabilities. The older ALTabletService failure remains unexplained; the new route works without it.

### Fictional floor-plan display on physical tablet
Operator confirms the browser preview looks good on Pepper. This demonstrates rendering of the bundled fictional floor-plan UI in tablet Chrome. Touch-route acceptance is pending explicit confirmation; native version 0.2 update remains blocked by an observed Play Store crash. No real building plan, robot localization or movement is demonstrated.

### Floor-plan browser acceptance passed — November 3
Operator explicitly confirmed tapping Lab and Classroom changes both the blue route and written directions on Pepper. Fictional floor-plan rendering and touch interaction are now physically verified in tablet Chrome. This is separate from the native 0.2 update, which remains uninstalled/unverified after Play Store crashes. No real building directions, localization, shortest-path navigation or robot movement were demonstrated.

Next implementation step: replace the fictional layout with a supplied real floor plan and verified destination/route data. In parallel, diagnose the Android update path using installation logs rather than repeated blind retries. Preserve the working native 0.1 app and browser preview as separate verified evidence.
