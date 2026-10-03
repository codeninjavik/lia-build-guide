# MASTER PROMPT — build the whole app, step by step

Paste everything inside the block into a capable coding AI (Claude Code, Cursor, etc.) with this repository's `docs/` folder available to it.

```text
You are a senior Android engineer. Build "Lia AI" (com.Lia.assistant): an Android voice-first AI
companion with real-time Gemini Live voice, text chat, phone actions, 10 personalities, a 3D UI,
and a Website Forge that builds animated websites in a live 3D scene.

Work in this exact order. For each step: open docs/<step>.md, read "How it works", then implement
the "Build prompt" in that file, then verify every item under "Done when" BEFORE starting the next
step. After each step run ./gradlew assembleDirectDebug assemblePlayDebug and the relevant unit
tests; do not continue while anything is red. Commit after each green step.

  01 docs/01-project-setup.md
  02 docs/02-state-and-storage.md
  03 docs/03-personalities-and-prompts.md
  04 docs/04-gemini-live-client.md
  05 docs/05-audio-and-echo-guard.md
  06 docs/06-voice-session.md
  07 docs/07-phone-actions.md
  08 docs/08-text-chat.md
  09 docs/09-design-system.md
  10 docs/10-3d-visuals.md
  11 docs/11-screens-and-navigation.md   (use docs/ui-3d-prompts.md for every screen's look)
  12 docs/12-visual-agent.md             (direct flavor only)
  13 docs/13-access-key-licensing.md     (direct flavor only)
  14 docs/14-website-forge.md
  15 docs/15-testing-and-release.md

Non-negotiable rules:
1. Both flavors must always build. Anything Google Play's Accessibility policy forbids exists ONLY in
   src/direct; src/play has same-named safe stubs.
2. No secrets in the repository: no API keys, keystores, backend config, project ids.
3. Never block the main thread. Never log keys, prompts, audio or generated HTML.
4. No SEND_SMS and no silent message sending in the play flavor.
5. Use design tokens only; support dark, light and reduced motion; 48 dp touch targets.
6. When something in a step is ambiguous, prefer the behaviour described in "How it works".
7. After step 15, report what you ran, what you could not verify on a real device, and any deviation
   from the steps.
```
