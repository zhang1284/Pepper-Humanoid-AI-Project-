# From discovery to implementation — November 3, 2026

The project has entered implementation. Discovery continues for specific robot interfaces; it is no longer a single gate that blocks all development. The target remains a Robohearts replacement, not a collection of diagnostic notebooks.

| Workstream | Established evidence | Next deliverable |
| --- | --- | --- |
| Tablet host | Team-built APK installed on physical Pepper; native/web rendering, switching and normal exit confirmed | Interactive fictional floor plan, then real supplied building assets |
| Speech and vision | Prior professor-derived harness produced audible AI replies and image answers | Integrate these functions into the new platform; establish repeatable latency/recovery |
| Knowledge/context | Team requires PDF and project-instruction context | Ingest documents with source/version records and evaluate grounded answers; initial implementation needs no robot |
| Native robot microphones | Service contracts inspected; raw robot capture unverified | A bounded local capture, playback and cleanup test; distinguish robot input from Mac microphone |
| Gestures | 246 standard animation paths and 23 tags inventoried | Validate selected gestures and reliable completion/cancellation before AI orchestration |
| Movement/navigation | Motion getters/protection values read; mapping app installed; physical navigation unverified | Verify supported interfaces, localization and stopping behavior, followed by agreed physical tests |
| Reliability | Real provider 503s observed; Mac local-network permission was a connection barrier | Explicit unavailable/busy states, provider abstraction, session capability refresh and truthful action results |

## Discovery exit criteria
An interface is ready to build against when its callable contract, actual data/command path, physical acceptance behavior and failure/cleanup behavior are demonstrated. Service presence and successful-looking acknowledgements alone do not meet this criterion. Tablet display now meets it for the standalone offline host. Other features progress independently.

## Version 0.2 floor-plan increment
Prepared a clearly labeled fictional layout bundled in the Android app. Four destinations can be selected with Android buttons or room taps, updating a drawn sample route and written instructions. No real-building coordinates, localization, remote content or robot actuation are involved. Existing native/web display checks and Close test remain. Compile/lint and package verification precede operator installation; physical touch/route validation remains a separate acceptance step.

### Floor-plan browser acceptance passed — November 3
Operator explicitly confirmed tapping Lab and Classroom changes both the blue route and written directions on Pepper. Fictional floor-plan rendering and touch interaction are now physically verified in tablet Chrome. This is separate from the native 0.2 update, which remains uninstalled/unverified after Play Store crashes. No real building directions, localization, shortest-path navigation or robot movement were demonstrated.

Next implementation step: replace the fictional layout with a supplied real floor plan and verified destination/route data. In parallel, diagnose the Android update path using installation logs rather than repeated blind retries. Preserve the working native 0.1 app and browser preview as separate verified evidence.
