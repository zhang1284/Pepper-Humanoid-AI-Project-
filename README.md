# Pepper + Gemini voice notebook

`pepper_gemini_voice2.ipynb` lets Pepper answer spoken questions through Gemini. Because this Pepper does not run the
RoboHearts web-socket service on port 8443, the notebook brings its own **bridge** (`bridge/`), which logs in to Pepper's
NAOqi 2.9 directly (`tcps://<PEPPER_IP>:9503`, official `qi` library) and offers the notebook the same connection on
`127.0.0.1:8443`. Keep the notebook, `bridge/` and `tests/` together in one folder.

## One-time setup (per computer)

1. Python for the notebook: `python3 -m venv .venv && .venv/bin/pip install -r requirements.txt`
2. The bridge's own Python + qi: `bash bridge/setup.sh` on macOS/Linux (Python 3.12 + qi 3.1.5), or `bridge\setup.bat` from an
   Anaconda Prompt on Windows (Python 3.6 + qi 2.0.1, the only Windows build; see INSTALL_WINDOWS.txt). The first notebook
   cell runs this by itself when `bridge/.venv` is missing. The wheels are in `bridge/wheels/`.
3. Only if Pepper's password is not the factory default `nao`: copy `bridge/.env.example` to `bridge/.env` and edit
   `PEPPER_PASSWORD`.

## Every time

1. `.venv/bin/jupyter lab` and open `pepper_gemini_voice2.ipynb`.
2. Set `PEPPER_IP` in the Step 1 cell to the address Pepper says when you press its chest button, and run the cell. It starts
   the bridge and prints `Pepper '<name>' is connected through the bridge`.
   The widget says *Failed to 127.0.0.1* the first time in a browser session: open `https://127.0.0.1:8443/check` in a new
   tab, click Advanced, then Proceed, and run Step 1 again. It then says *Connected to 127.0.0.1*.
3. Run the Step 1b cell: it must print `CONNECTED`. Otherwise it names the cause and the fix.
4. Run the Step 2 cell, paste your Gemini API key, press Wake up Pepper, then Start listening.

## What you can say (Step 2)

Press **Start listening**, speak, press **Stop & ask**. Right after you stop, Pepper takes a photo with its head camera; Gemini
hears the recording, sees the photo and decides what Pepper says and does.

| You say | Pepper does |
|---|---|
| "What is this object?" (hold it in front of Pepper's face, about half a metre away) | names it from the photo, adds a short fact |
| "Pepper, go forward five steps" | rolls forward 5 x 0.2 m = 1 m (`STEP_METERS`); at most 1.5 m per command (`MAX_FORWARD_M`) |
| "Lift right hand" / "Lift left hand" / "Raise both hands" | raises that hand, holds it 3 s (`HAND_HOLD_SECONDS`), lowers it |
| "Put your hands down" | lowers raised hands |
| anything else | answers it |

If Pepper does not roll, the chat says why, in Pepper's own words: "its safety system saw an obstacle about 0.5 m ahead"
(clear the floor in front, including chair legs and table edges) or "Pepper keeps its wheels switched off" (close the charging
flap at the back of its base and unplug the charger; Pepper locks its wheels while the flap is open). The notebook's first live
run on 2026-10-14 ended with the second message: the flap was open.

Safety: Pepper moves only when you clearly ask. A recording without speech is not sent, and before a movement a second,
independent transcription must contain the words for it ("forward"/"steps", "right"/"left" with "hand"/"arm", "hands down").
Pepper's own obstacle protection stays on; **Stop moving** stops the wheels at once; untick **Allow Pepper to move**
to keep it still. Keep about 1.5 m clear in front of Pepper and close its charging flap. The camera can be switched to the
laptop's webcam or off. The bridge carries out the movements (`PepperSkills` in `bridge/pepper_bridge.py`) and enforces the
1.5 m limit and Pepper's joint limits itself.

## Tests (no robot needed)

* `python3 tests/test_skills.py` - the bridge's camera, forward move, hands and stop against a simulated Pepper
  (`tests/fake_naoqi.py`, built with the real `qi` library; it refuses joint angles outside Pepper's limits).
* `node tests/test_speech_gate.mjs` - the movement safety gate: noise, a click and silence are never sent, and (with
  `GEMINI_API_KEY=...`) a second, neutral transcription confirms every real command and blocks every invented one.
* `GEMINI_API_KEY=... node tests/test_gemini_intents.mjs` - sends Gemini the notebook's exact request with the recordings and
  photos in `tests/assets/` and checks the action, the steps and the object named (needs Node 18 or newer).
* `python3 tests/test_connection.py` - the bridge, the Step 1b check cell and the Step 2 connection code. Add
  `--robot <PEPPER_IP>` to also print the check cell's verdict for the real robot.
