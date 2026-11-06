# Live Pepper capability discovery — November 2, 2026

Scope: fresh read-only calls through the already connected Robohearts JupyterLite/ipynao widget. No robot movement, gestures, camera/audio capture, tablet display change, settings write, package install, or cloud-model call was made during this audit.

## Connection evidence
The Mac's direct attempts to NAOqi ports 9503 and 9559 at user-confirmed <local-network-IP> returned No route to host. The local bridge reported disconnected. The existing Robohearts browser widget subsequently completed fresh ALSystem.systemVersion and ServiceDirectory.services requests. Therefore direct native access failure must not be described as proof that all routes to Pepper are unavailable. The browser gateway and native connection are separate paths; the cause of their differing results remains unresolved.

Fresh software version: **2.9.5.172**.

## Findings and their implications

| Area | Directly observed | Implication and next test |
|---|---|---|
| Base motion interface | ALMotion.getMethodList lists move, moveToward, moveTo, moveAlong, stopMove, moveIsActive and position getters. | Candidate interface exists for more than gestures. Actuation, odometry accuracy and stopping are NOT yet tested. |
| Movement status | moveIsActive returned false. | No active movement reported at the time of this sample. |
| Collision protection | getExternalCollisionProtectionEnabled("Move") returned true. | Existing movement collision protection was enabled; no setting changed. Not proof that all obstacles will be detected. |
| Security distances | Orthogonal approximately 0.4 m; tangential approximately 0.1 m. | Current configured values, not measured stopping distances or a guarantee of clearance. |
| moveTo contract | getMethodHelp returned x/y positions in metres, theta around z in radians; blocking call; return description says true if finished successfully. | We now have a robot-returned contract for a future explicitly authorized small movement test. No moveTo call was made. |
| Audio | ALAudioDevice method list includes subscribe/unsubscribe, getParameter/setParameter, startMicrophonesRecording/stopMicrophonesRecording. | Recording/streaming entry points exist, but callback transport, channel handling and cleanup need inspection before capture. No microphones were started. |
| Navigation | Public method list contains computePathInMap; other map/localization names observed were prefixed with underscores. Legacy public navigateTo/explore/localize names were not in this method-list result. | Cannot assume older navigation examples apply to this 2.9 installation. Investigate supported QiSDK/navigation interfaces. Do not invoke private methods just because they are visible. |
| Gestures/behaviors | ALBehaviorManager query returned “not available.” | This particular route does not expose the expected legacy behavior manager; does not prove installed animations or gestures are impossible. Investigate ALAnimationPlayer and supported alternatives. |
| Tablet | ALTabletService getMethodList returned “getMethodList does not exist for ALTabletService.” | Legacy ALModule introspection is not a universal service-discovery mechanism. This result says nothing yet about whether showWebview/showImage or map display will work. Need service-specific schema and a controlled display test. |

## Evidence preservation
Read-only audit cells were added to the existing intro1_setup.ipynb, separately from the supplied speech example. Results are retained in that notebook. A JSON audit was downloaded as `Pepper-live-capability-audit-2026-11-02.json` to the user's Downloads folder. macOS denied the assistant's attempts to read that downloaded file, including an escalated attempt; its full contents have NOT been independently copied/verified in this project. This report records the results visible in the notebook UI. No credentials or raw participant media are included.

## Next focused investigation
1. Tablet-specific schema/read-only status, then an explicitly approved simple display test before attempting a floor plan.
2. Audio subscription/callback contract and native robot microphone playback.
3. Animation/gesture alternatives for NAOqi 2.9.
4. Supported localization/navigation interfaces; distinguish a relative base movement from navigating a building.
5. Only then, propose a small bounded physical action with clear preconditions, stop path, and observed completion.

Primary reference for the legacy introspection methods (not a guarantee every 2.9 service implements them): https://doc.aldebaran.com/2-1/naoqi/core/almodule-api.html . Robot-returned methods/help take precedence over assuming an older API is installed.
