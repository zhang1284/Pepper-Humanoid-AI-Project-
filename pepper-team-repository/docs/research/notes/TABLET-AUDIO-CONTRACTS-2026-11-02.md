# Tablet and robot-audio contracts — November 2, 2026

Fresh read-only requests through the connected Robohearts ipynao widget. Results are retained in detail_tasks/detail_results in the notebook. Initial metadata requests did not record audio or change preferences. The subsequent image-display test and cleanup are recorded below; no reset or webview load was performed.

## Tablet observations

| Getter | Observed value | Interpretation |
| --- | --- | --- |
| ALTabletService.getWifiStatus() | CONNECTED | Service reports a tablet Wi-Fi connection; does not prove any particular URL is reachable. |
| ALTabletService.robotIp() | 198.18.0.1 | Tablet-side robot address returned by this installation. Distinct from the head's campus address. |
| ALTabletService.version() | 6.0.21 | Tablet service version, not Android version. User-supplied About screen previously established Android 6.0. |

Legacy getMethodList is not exposed on this tablet service. The service is nevertheless responsive to these specific getters. Do not infer that display is unavailable from the introspection failure.

### Display design implications
The older vendor API describes showImage/showImageNoCache, loadUrl and showWebview. A loadUrl call alone does not display the webview. Its examples host robot-side content under the tablet-facing 198.18.0.1 address. These are candidate contracts to validate on this robot, not proof that all methods/assets work on NAOqi 2.9.

A localhost URL on the Mac is not the Mac's address from Pepper's tablet. An externally hosted URL adds DNS, internet access, TLS trust and browser-version compatibility dependencies. Start by separating a known local image, a minimal local HTML page, and the actual floor-plan asset. Do not weaken TLS validation to make a page load.

Proposed adapter: distinct show_image, show_page and clear_display tools; explicit success/failure; confirmed reachable asset URLs. If the deployed tablet supports them, page/image completion/error signals should be captured instead of equating an accepted command with visible content.

Source: https://doc.aldebaran.com/2-5/naoqi/core/altabletservice-api.html . This is older API documentation; use live results to establish compatibility.

## Robot microphone observations
Read-only getMethodHelp succeeded for the following methods:

- subscribe: accepts a module/client name. A named audio receiver is required; this is not a Python function that simply returns a WAV.
- setClientPreferences: arguments are name, sampleRate, channelsConfiguration, deinterleaved. Robot-returned help specifies 16000 or 48000 Hz; channel options include all, left, right, front and rear. Layout flag distinguishes interleaved from deinterleaved samples. This method itself was NOT called.
- startMicrophonesRecording: accepts a filename; returned help describes front-microphone OGG or four-channel, 16-bit little-endian, 48 kHz WAV. This method itself was NOT called.

Important consequences:
1. A recording-file path does not establish a supported way to fetch that file from the robot.
2. A live callback requires a client object/service reachable by Pepper. A browser-only notebook may not provide that interface directly; investigate the gateway or a native receiver.
3. Four-channel robot WAV must not be treated as mono or mislabeled 16 kHz. This is separate from the Mac browser's capture defect.
4. Future capture test must have an explicit start, short duration, known data destination, local playback and unsubscribe/cleanup. Do not subscribe speculatively during metadata discovery.

Vendor reference: https://doc.aldebaran.com/2-5/naoqi/audio/alaudiodevice-api.html . The specific values above were also returned by this robot's method-help interface.

## Animation observation
ALAnimationPlayer.getMethodList is not exposed by its proxy. That is an introspection limitation, not an animation execution test. The older vendor design links AnimationPlayer to BehaviorManager, which was unavailable in the earlier query. QiSDK's animation factory/Animate interface is an alternative to investigate for NAOqi 2.9; it has not been demonstrated in this project.

Sources: https://doc.aldebaran.com/2-5/naoqi/motion/alanimationplayer.html and https://android.aldebaran.com/sdk/doc/javadoc/qisdk/alltypes/index.html . No animation paths or tags have been executed.

## First tablet display command
After the operator reported Android home as the baseline and was told to watch the tablet, sent one `showImage('http://198.18.0.1/img/help_charger.png')` request. The fresh returned value was boolean **true**. Physical visibility is pending operator confirmation. The image has not yet been hidden; it is intentionally left visible for observation. This is a display change only; no robot movement, recording or Wi-Fi changes. Do not classify the display path as functionally verified until the operator confirms the physical result.

### Operator result and cleanup
The operator did NOT see the charger-help image. They reported slowly revolving colorful dots near the center of the screen. Therefore this test is **not a successful image-display demonstration**, despite showImage returning true. The cause remains unknown (asset, loading, rendering or activity/display ownership are possibilities, not established diagnoses).

Sent hideImage() as the planned cleanup; its result was boolean true. Physical cleanup result is pending operator confirmation. A read of ALMemory's existing ALTabletService/error event returned boolean true, without descriptive error text; it does not identify the failure cause. No reset, Wi-Fi change, cache clearing or additional image loads were attempted.

Next investigation should observe tablet load/completion/error signals or inspect the tablet activity/asset-serving path. Avoid repeating unrelated images or interpreting command acknowledgement as visible success.

### Physical cleanup confirmation and follow-up
The operator confirmed the colorful revolving dots **remained after hideImage returned true**. Returning to Android home was not demonstrated. This is an unresolved display-state problem; neither a broken tablet nor a loading-state diagnosis is established.

Fresh status reads after cleanup:
- getWifiStatus(): CONNECTED (does not prove asset reachability).
- getCurrentLifeActivity(): method not exposed by ALTabletService on this installation.
- ALMemory.getData('ALTabletService/message'): Data not found.

These reads did not identify the active Android application or the reason for the dots. The older vendor activity getter cannot be used here. A next controlled display test needs load/error signal support or Android-side activity diagnostics before another asset request. No reset, application restart, or network change was attempted.

### Recovery correction from operator
The operator subsequently touched the tablet and confirmed it returned to the normal Android home screen. Recovery is therefore physically confirmed after touch. We cannot attribute that transition to hideImage alone or determine what the dots represented from this observation.

### Transport caveat found during source inspection
The installed ipynao frontend's callService handler sends `data: resolution ?? true`. Consequently, a Python boolean true may represent an actual true return OR a null/undefined result converted by the frontend. Prior true observations (including the error memory event) must be treated as wrapper results, not verified raw robot booleans. This further supports requiring visible confirmation and explicit completion/error signals. Getter strings and explicit method-not-found errors are unaffected by this particular conversion.

Local source inspected: ipynao labextension widget source map, callService handler. No credentials were inspected.

## Raw service metadata breakthrough
A short separate browser WebSocket to the existing robot gateway successfully retrieved ALTabletService and ALAnimationPlayer metadata with ServiceDirectory.service, then closed. No authentication credentials were read or transmitted by the diagnostic, and no operational robot method was invoked on that socket.

Live tablet metadata confirms showImage(string) -> bool; hideImage() -> void; showWebview() / showWebview(string) -> bool; hideWebview() -> bool; loadUrl(string) -> bool. In particular, hideImage's Python true was synthesized by the notebook connector. The live schema contains onImageLoaded(), onPageStarted(), onPageFinished(), onLoadPageError(int,string,string), onConsoleMessage(string), and onJSEvent(string). Presence is confirmed; signal delivery has not yet been functionally tested.

This establishes a practical next tablet test design: connect completion/error listeners first, issue one controlled asset request, record raw response and signal timestamps, ask the observer what appeared, then disconnect listeners and restore the display. A returned bool alone is insufficient.

### Installed animation inventory
Using the now-confirmed read-only inventory methods _getAnimations() and _getTags() returned 267 path entries and 23 tags. Of these, 246 paths are under animations/Stand: Affirmative 52, Enumeration 33, Exclamation 40, Listening 2, Negation 21, Question 22, SelfAndOthers 33, SpaceAndTime 43. Another 21 entries have other package paths; do not count all 267 as validated user-facing gestures.

Public execution methods run(string) and runTag(string) are exposed, both returning void. Neither was called. The inventory methods are underscored implementation interfaces, so a production adapter should isolate them and capability-check them rather than assume universal support. Full lists remain in the live notebook's gesture_inventory variable; a selected structured record is saved in research/tablet-animation-interface-2026-11-02.json.

## Controlled image-preload check
A subsequent short diagnostic socket failed to connect after 0.3 seconds, before registering listeners or calling preLoadImage. This attempt therefore did not test event delivery. Its connection was closed. The existing notebook connection independently returned systemVersion 2.9.5.172, so this was not a universal loss of robot access.

Via the still-live notebook connection, preLoadImage('http://198.18.0.1/img/help_charger.png') returned **false**. Unlike null, false is preserved by the connector's null-coalescing conversion. The service thus explicitly reports a preload failure for this candidate asset. The older vendor example URL is not established as available on this installation. Cause remains unknown; neither a missing file, unreachable internal server, nor decoder failure is proven.

No image/page was shown in this follow-up. No reset, settings changes, motion or recording were performed. This result changes the next test: first establish a reachable valid asset or self-contained page on this installation, then test visible rendering with completion/error observations. Do not keep using the charger-help asset as if it were a known-good control. Structured evidence: research/tablet-preload-test-2026-11-02.json.

## Embedded image and Android application inventory
An independently generated and locally viewed 96x96 blue-and-white PNG was sent as a data:image/png;base64 URI to preLoadImage. The result was false. Data-URI support in this method is not established, so this is not a definitive renderer test. No show command was sent.

Read-only implementation-specific getters then returned:
- _ping(): pong.
- _getTabletModelName(): LPT_200AR.
- _listApks(): a space-separated list of 124 installed packages.

Selected _getApkVersion results:

| Package | Installed version |
| --- | --- |
| jp.softbank.tabletbrowser | 6.0.21 |
| com.aldebaran.robotservice | 2.3.2 |
| com.google.android.webview | 95.0.4638.74 |
| com.softbankrobotics.maplocalizeandmove | 1.4.7 |
| ai.robohearts.pepperchat.signed | 1.7.3 |

The tablet is responsive to direct requests. The browser and mapping/localization application are installed; their foreground state and functional behavior remain unverified. No application was launched, stopped, updated or reconfigured. Mapping-app presence does not prove that a usable map exists or navigation works. The full package list and parsed versions remain in notebook variables; selected evidence is saved in research/tablet-application-inventory-2026-11-02.json.

Design consequence: inspect tablet-browser behavior as a specific installed component instead of treating Android home or an accepted robot RPC as proof of rendering. Keep the installed map/localize app as a separate navigation investigation lead; do not launch it during tablet troubleshooting because its side effects are not yet established.

## Blank-webview and blue-page test
The next event-enabled socket failed before display commands; live signal delivery remains unverified. Existing notebook fallback called showWebview('about:blank'), then executeJS to replace the page with a simple blue PEPPER DISPLAY TEST layout. Both produced Python true, with the previously documented null-coercion caveat (executeJS has a void signature). The observer reported Android home changed to revolving colored dots; the blue layout was not seen.

A scheduled hideWebview request ran after 40 seconds and returned false. The cleanup timer itself completed, but physical restoration is not confirmed. Do not mark the test cleaned up based only on the timer.

The operator suggested the dots might represent sleep. This is a plausible idle/screensaver hypothesis, not established fact. Older official documentation separately describes an idle screen shown after hideWebview and tablet standby controlled by goToSleep/wakeUp. Sent ALTabletService.wakeUp only; no ALMotion, posture, robot-wide wake, reset, network or persistent settings command was sent. Physical response is pending operator observation. Source: https://doc.aldebaran.com/2-5/naoqi/core/altabletservice-api.html .

## Operator clarifies exact recovery interaction
The tablet-only wake command caused no visible change: dots remained. The operator then clarified that recovery is NOT a single touch: they swipe up from the bottom to reveal Android navigation controls, then press the circle (Home), which immediately returns to Android home.

This supersedes the earlier shorthand that touching the screen restored home. Android navigation and Home respond normally. The observation supports the interpretation that the dots belong to a foreground application/view, rather than establishing that the tablet was asleep. The exact foreground package and whether the dots are that application's idle/loading UI remain unknown. Next discriminating observation: inspect the recent-app card label without closing, force-stopping or relaunching any app.

## Recent Apps observation and native-dialog control
The operator reported only Robohearts and Settings in Android Recent Apps; no separate card with the dots. Neither app was closed. The exact foreground component that draws the dots remains unidentified; absence from Recent Apps does not establish which app owns it.

Fresh native Mac TCP checks for head address <local-network-IP> on ports 8443, 9503 and 9559 all returned OS error 65 (No route to host). Existing notebook transport has previously returned live getter values. A local notebook diagnostic found no exported jupyterapp/jupyterlab/require globals; we did not attach event listeners to or alter the existing widget connection.

A different control is now under test: ALTabletService.showInputTextDialog('PEPPER DISPLAY TEST', 'OK', 'Cancel'), which requests a native Android text dialog rather than image or web-page loading. No typing is needed. A hideDialog cleanup request is scheduled after 30 seconds. Physical observation and completion results are pending. The three-string dialog method and hideDialog are present in this robot's live service metadata, and the vendor describes this overload as title/OK/Cancel: https://doc.aldebaran.com/2-5/naoqi/core/altabletservice-api.html . This is a temporary display test; no app closure, reset, network change, recording or actuation.

Native-dialog follow-up: request wrapper result true; cleanup timer completed; hideDialog wrapper result true. Both methods have void returns, so these true values are synthetic acknowledgements. Physical display and dismissal remain pending observer confirmation. No success claim is made.

## Session stopping point — operator leaving for class
The operator did not confirm seeing the native dialog. They reported dots again and occasional flickering that might correlate with commands, explicitly uncertain of that correlation. Native dialog rendering is therefore not functionally verified. The cleanup timer completed and hideDialog returned the previously documented synthetic acknowledgement; physical dismissal remains unconfirmed.

Live testing stopped at the operator's request. No physical motion, microphone recording, reset, app closure/installation, or network/settings changes were performed in these tablet tests. No diagnostic display timer remains pending according to the inspected notebook timer states. The normal existing robot connection was left intact. If dots remain, the operator's established Android Home navigation provides manual recovery.

Resume with the saved evidence and existing notebook state if still available; if the Mac sleeps or the connection is lost, re-establish a fresh read-only connection before any display test. Priorities: (1) establish a stable event-capable connection without disrupting the current route, (2) identify actual foreground tablet/browser state, (3) validate one native or web display with operator observation. Do not repeat the same speculative asset tests or infer that a reset is needed.
