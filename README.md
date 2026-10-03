# LIA AI — Build Guide (step by step, with prompts)

LIA is an Android voice-first AI companion. You talk to it in Hindi, Hinglish or English, in real time, and it can open apps, call and message people, chat in text, and even **build a full animated website in front of you inside a 3D "Forge" scene**.

This guide tells you **what each feature is, how it works, and gives a copy-paste prompt for every step** so you can rebuild the whole app with any capable coding AI (Claude Code, Cursor, etc.), one step at a time.

> It documents the real app (`com.Lia.assistant`, Kotlin + Jetpack Compose). No API keys, signing keys or private config are in this repo.

## How to use this guide

1. Read [`docs/00-architecture.md`](docs/00-architecture.md) once. It has the big diagram.
2. Go through the steps **in order**. Every step file has the same four parts:
   **Feature** → **How it works (logic + diagram)** → **Build prompt (copy-paste)** → **Done when (checklist)**.
3. Give your coding AI the step prompt, let it build, run the checklist, then move to the next step.
4. For the 3D / premium look of every screen, use [`docs/ui-3d-prompts.md`](docs/ui-3d-prompts.md) alongside steps 9–11.
5. Short on time? [`MASTER-PROMPT.md`](MASTER-PROMPT.md) tells the AI to run all steps in order by itself.

## Steps

| # | Step | What you get |
|---|------|--------------|
| 01 | [Project setup & flavors](docs/01-project-setup.md) | Gradle, Compose, `direct` and `play` flavors |
| 02 | [State, branding & settings storage](docs/02-state-and-storage.md) | API key store, preferences, app state |
| 03 | [Personalities, language & prompt builder](docs/03-personalities-and-prompts.md) | 10 personalities, Hindi/Hinglish/English detection, memory recap |
| 04 | [Gemini Live client](docs/04-gemini-live-client.md) | Real-time WebSocket voice protocol |
| 05 | [Audio I/O & echo guard](docs/05-audio-and-echo-guard.md) | Mic, speaker, no self-hearing |
| 06 | [Voice session & foreground service](docs/06-voice-session.md) | Conversation lifecycle, reconnect, latency metrics |
| 07 | [Phone actions (tools)](docs/07-phone-actions.md) | open app, call, message, tap, type, scroll |
| 08 | [Text chat](docs/08-text-chat.md) | Gemini REST chat with self-healing model lookup |
| 09 | [Design system & theme](docs/09-design-system.md) | "Pearl of light in the night" tokens, depth, components |
| 10 | [3D visuals: orb, splash, edge glow](docs/10-3d-visuals.md) | AGSL 3D sphere, Canvas orbs, 3D splash |
| 11 | [Screens & navigation](docs/11-screens-and-navigation.md) | Home, Voice, Chat, History, Settings and more |
| 12 | [Visual social-media / WhatsApp agent](docs/12-visual-agent.md) | Accessibility-driven task agent with confirmations |
| 13 | [Access-key licensing](docs/13-access-key-licensing.md) | Online access-key gate (`direct` only) |
| 14 | [Website Forge (3D build scene)](docs/14-website-forge.md) | "Lia, ek cafe ki website banao" → live 3D build → finished site |
| 15 | [Testing, release & Play Store](docs/15-testing-and-release.md) | Tests, signing, store listing |

## The two builds (flavors)

| | `direct` (website / sideload) | `play` (Google Play) |
|---|---|---|
| Voice, chat, Forge, personalities | yes | yes |
| open app, call, message (user taps Send) | yes | yes |
| Screen control (read, tap, type, scroll) via AccessibilityService | yes | **removed at compile time** |
| Instagram / Facebook / WhatsApp visual agent | yes | **removed at compile time** |
| Access-key licensing | yes | no |

Google Play restricts the Accessibility API, so the `play` build physically does not contain those classes.

## Tech at a glance

Kotlin · Jetpack Compose · Material 3 · OkHttp (WebSocket + SSE) · `org.json` · DataStore · AGSL `RuntimeShader` (3D orb) · WebView + Three.js (Forge scene) · Gemini Live API (voice) · Gemini `generateContent` (chat, websites).

## License

Documentation only. Use it freely to build your own assistant.
