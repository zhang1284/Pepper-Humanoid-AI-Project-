# Native Android display experiment

## Why this experiment

The full goal remains a Robohearts replacement: knowledge from documents and project instructions, vision, tablet floor plans, extensible gestures, and validated AI-requested movement/navigation. This experiment establishes a display host for that platform; it does not replace that goal with another conversation harness.

November 3 evidence: the robot responds on a fresh notebook connection, but its current 74-service registry lacks ALTabletService. The physical tablet reports a USB connection to Pepper and runs Android home. November 2 image, page, and native-dialog requests did not yield visually confirmed content. The cause is not established. Continuing the same legacy display calls is not the only route available.

The official Pepper SDK supports an Android application on the tablet: https://android.aldebaran.com/sdk/doc/pepper-sdk/index.html . First isolate Android rendering without adding robot SDK, robot focus, model/provider, networking or movement dependencies.

## Prepared artifact

Source project: ../../pepper-android-display/ (sibling of pepper-application).

- Separate package edu.creighton.pepper.displaylab, label Pepper Display Lab.
- Blue native Android test panel.
- Green, self-contained WebView test panel with a page-load status.
- Native / Web page / Close test buttons; standard Android navigation available.
- No Android permissions or third-party app dependencies; no robot actuation interface.
- Android 6.0 minimum. Native and WebView paths use the actual tablet hardware when installed.

## Interpretation and next steps

| Physical result | What it establishes | What remains unknown |
| --- | --- | --- |
| Native blue panel works | Our own Android Activity can render on this tablet | Legacy service failure; robot SDK integration |
| Native works, embedded green panel fails | Native display route works; isolate WebView/provider behavior | Precise WebView cause |
| Both work | Native Android host offers a usable display route | Live floor-plan assets, touch interaction, robot/AI integration |
| App cannot install or launch | Tool/device compatibility requires investigation | Rendering is not yet tested |

After physical confirmation, add a bundled sample floor plan and touch interaction, then integrate typed robot capabilities separately. Document/model adapters can proceed independently. Real navigation still requires localization, obstacle handling and explicitly agreed physical tests.

## Current build status

Project source prepared; manifest XML parsed and shows zero permissions, services or receivers. No JDK / Android SDK found at checked local paths. No APK compiled, installed or physically tested. Android Gradle Plugin 8.9.2 / Gradle 8.11.1 / platform 35 tool compatibility verified against official documentation: https://developer.android.com/build/releases/agp-8-9-0-release-notes .

Google requires Android SDK license acceptance before command-line tool download. User was asked to review the official terms: https://developer.android.com/studio#command-tools . No agreement accepted by the agent. Device installation/debugging authorization will be addressed after a build exists; USB tethering remains unchanged.

## Build completed
User approved the SDK license. Downloaded project-local tools and built the app successfully. Android lint: 0 errors, 2 non-blocking warnings documented in the sibling project’s BUILD-VERIFICATION.json. Packaged permissions: zero. APK v1/v2 signatures verified. Final compile/target API is 35 with minimum 23. Artifact: ../../pepper-android-display/Pepper-Display-Lab-0.1.apk. It has not been installed or physically tested. Installation path is pending the operator’s tablet Wi-Fi IP; no debugging or tethering changes have been requested.

### Embedded web display confirmed — November 3
After the photographed blue native panel, the operator tapped Web page and confirmed the green embedded web panel was visible. Native Android and in-app WebView rendering are now physically demonstrated on Pepper using the standalone APK. This is an offline bundled-content demonstration, not yet a floor-plan asset, external web connectivity or robot-control integration test. Return-to-native and Close test recovery remain pending.

## Display acceptance passed — November 3, 2026
The operator confirmed the entire intended sequence worked: blue native panel, green embedded web panel, return to the native panel, and normal app closure. This establishes a physically tested standalone Android display host on the existing Pepper tablet. No robot motion or model connection was part of this test.

Next milestone: a bundled, touch-interactive floor-plan demonstration using this app, with clearly marked sample content until a real building plan is supplied. Displaying directions and navigating the physical robot remain separate capabilities. The older ALTabletService failure remains unexplained; the new route works without it.
