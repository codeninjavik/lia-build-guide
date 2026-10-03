# PROMPT 15: Tests & Release

## Ye prompt kya banayega

- Saare pure logic ke unit tests.
- Real phone par smoke-test ki script.
- Signed release build aur Play Store checklist.

**Pehle ye prompts ho chuke hone chahiye:** [01](01-project-setup.md)

**Kaise use karein**
1. Neeche wale code box ke **copy button** se poora prompt copy karo.
2. Apne AI coding tool me paste karo (Claude Code, Cursor, Copilot Chat, ChatGPT ya koi bhi).
3. AI jab bole ki ho gaya, "Check karo" list se verify karo. Sab theek ho to agla prompt.

## Copy this prompt

```text
Add the test suites and release setup for Lia AI.
1) JVM unit tests (JUnit4, kotlinx-coroutines-test, org.json) for every pure component listed in the
   table, split into src/test (common), src/testDirect and src/testPlay. Use fakes instead of mocks
   for ScreenDriver and clocks. Run with ./gradlew testDirectDebugUnitTest testPlayDebugUnitTest.
2) Release: proguard-rules.pro keeping org.json and Compose needs; release buildType with
   isMinifyEnabled and isShrinkResources; signingConfig read from keystore.properties if present
   (git-ignored); versionCode/versionName in defaultConfig.
3) A docs/PLAYSTORE_LISTING.md with: artifact to upload (play AAB), listing copy, data-safety
   answers, permission justifications (microphone foreground service, contacts, call), and the
   submission checklist.
4) A DebugScreen ("Voice Pipeline Debugger") that shows connection state, selected personality,
   configured and detected language, VAD, mic state and per-turn latency from VoiceLatencyTracker.
Do not add any secrets to the repository.
```

## Check karo ki ban gaya

- [ ] Both unit-test tasks are green.
- [ ] `assemblePlayRelease` produces a signed bundle without any accessibility declarations.
- [ ] The smoke script passes on a real device (emulator microphones are unreliable).

Logic, diagrams aur samjhane wali detail: [`docs/15-testing-and-release.md`](../docs/15-testing-and-release.md)
