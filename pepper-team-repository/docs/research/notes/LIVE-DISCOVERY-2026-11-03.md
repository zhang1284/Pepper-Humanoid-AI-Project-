# Live Pepper investigation — November 3, 2026

## Session baseline
Operator is with Pepper, reconfirmed head IP <local-network-IP>, and reports Android home on the tablet. Yesterday's notebook remains open; existing output is historical evidence, not a fresh connection demonstration.

Fresh native TCP checks to ports 8443 and 9503 each returned OS error 65 (No route to host). No network configuration or permission changes were made. Read-only ALSystem.systemVersion and ALTabletService._ping requests were queued through the existing notebook; results are pending. No new display, recording, reset or movement commands sent during session initialization.

## Objective
Establish current connectivity before any visible test; then obtain tablet foreground/loading evidence. Avoid repeating failed speculative images or equating synthetic true acknowledgements with physical rendering. Baseline and tests will be recorded separately from November 2 findings.

## Fresh connection and service registry
Created a separate ipynao widget pepper_1103 to the confirmed head IP, retaining the older widget and notebook evidence. Fresh ALSystem.systemVersion returned 2.9.5.172. ALTabletService lookup failed with Cannot find service 'ALTabletService' in index.

An independent ServiceDirectory.services request returned 74 registrations. The extracted name list explicitly does not contain ALTabletService. Selected registered services include ALNavigation, ALRobotHealthMonitor, ALRobotModel, ALRobotMood, ALRobotPosture and ALSystem. This is stronger evidence than a failed display request: the tablet API is absent from today's queried registry. It does not prove the package is uninstalled, the tablet is disconnected, or a specific app must be restarted. Yesterday's tablet-browser/package metadata remains historical evidence only.

Old-widget concurrent getter errors included a tablet-service error under the system-version label, so those initial errors should not be used as clean per-method evidence. Fresh serialized system-version and service-registry reads above establish the current result. No new display requests, app launches, resets, movement or configuration changes were made.

## Next physical observation
Requested the robot-service notification status and tablet IP from the Android notification shade. No request to enable debugging, change Wi-Fi, accept certificates or close applications was made. Official QiSDK instructions identify the notification shade as a source of the tablet IP, separate from the robot connection: https://android.aldebaran.com/sdk/doc/pepper-sdk/ch1_gettingstarted/running_application.html . Observation pending.

## Physical notification evidence
Operator supplied a photo of the Android notification shade on November 3. It explicitly reads “Connected to Robot” and “Connected to Pepper via USB.” Android home is visible beneath it, with a RoboHearts AI 1.7.3 shortcut. No tablet IP address is visible in the supplied image. A separate app-permission-management notification is present; it is not itself evidence of a denied permission.

Interpretation: the tablet reports a USB robot connection. This must be distinguished from the fresh head registry lacking ALTabletService. We cannot diagnose physical tablet disconnection or a tablet Wi-Fi problem from that missing service. USB transport is observed; display API ownership/startup and compatibility remain unresolved. Next observation: inspect the existing Connected to Robot notification's details without changing connection or debugging settings.

Photo reference supplied by operator: [operator photo retained locally] . Visible notification text was inspected in chat; no identity, credential or unrelated notification data was requested.

## Notification tap opens USB tethering settings
A second operator photo shows the Android Tethering settings page, reached by tapping Connected to Robot. The USB tethering row is greyed/low-contrast; accompanying text describes USB as necessary to connect with Pepper. The photo alone does not establish the toggle state or whether the row is deliberately system-managed. No change was requested or performed. This screen provides no browser-rendering diagnosis. Operator should leave its switch unchanged and return Home.

Photo: [operator photo retained locally] .

## Native app transfer preparation and Mac permission
Tablet Wi-Fi IPv4 address was read from the operator's Advanced Wi-Fi photo: <local-network-IP>. Tablet reports connected to CUWireless. This is distinct from head IP <local-network-IP>. Mac Wi-Fi address: <local-network-IP>.

Initial native socket checks to both addresses returned No route to host. Read-only process ancestry identified the tools as /Applications/ChatGPT.app/Contents/Resources/codex-cli/CodexCLI.app/Contents/MacOS/codex beneath ChatGPT.app. System Settings showed ChatGPT Local Network access off. The operator explicitly approved enabling this one permission. Clicked the observed ChatGPT switch; the permission list redrew and app row identities changed, so visual confirmation of the final named switch state was not obtained. Subsequent nc to head port 8443 succeeded, and a tablet port-5555 check returned Connection refused instead of No route to host. Python's tablet-5555 check timed out. These results establish a changed native network path, not universal reachability or an available ADB service. No Python/Chrome permissions, tethering, tablet debugging or other network settings were intentionally changed.

Built and verified the standalone Pepper Display Lab APK (see sibling pepper-android-display/BUILD-VERIFICATION.json). Started a 15-minute temporary HTTP transfer server bound to Mac Wi-Fi port 8893, allowing only the tablet and Mac peers. Only the verified APK and a minimal download page are served; the project directory is not exposed. Local GET checks returned 200, and served APK SHA-256 matched the built artifact. Asked operator to download/open it in tablet Chrome, stopping at the installer or blocked-install screen. No install command, ADB authorization or unknown-sources change has occurred. A server log of APK transfer includes the local verification request, so it is not proof of a tablet download.

## Display acceptance passed — November 3, 2026
The operator confirmed the entire intended sequence worked: blue native panel, green embedded web panel, return to the native panel, and normal app closure. This establishes a physically tested standalone Android display host on the existing Pepper tablet. No robot motion or model connection was part of this test.

Next milestone: a bundled, touch-interactive floor-plan demonstration using this app, with clearly marked sample content until a real building plan is supplied. Displaying directions and navigating the physical robot remain separate capabilities. The older ALTabletService failure remains unexplained; the new route works without it.
